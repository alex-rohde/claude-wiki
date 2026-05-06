# T-SQL Automated Testing with Docker

This page covers how to set up a local SQL Server instance using Docker so Claude Code can write and run tests against your stored procedures directly. This removes the copy/paste loop and lets Claude execute real SQL, verify results, and iterate without SSMS.

---

## Why Docker for T-SQL testing?

The other T-SQL pages note that Claude Code cannot connect to SQL Server directly. That is true for remote or production databases, but with Docker you can spin up a local SQL Server instance that Claude Code can reach from the terminal. This unlocks a fully automated test loop:

1. Claude writes the test
2. Claude runs it via `sqlcmd` inside the container
3. Claude reads the output and verifies the result
4. Repeat until passing

No copy/paste. No SSMS required for testing.

---

## Prerequisites

- **Docker Desktop** installed and running on your Mac
- **Claude Code** open in your SQL work folder

### Install Docker Desktop

```bash
brew install --cask docker
```

After installation, open Docker.app from your Applications folder once to complete setup. Wait for the whale icon in the menu bar to show "Docker Desktop is running" before continuing.

---

## Set up the SQL Server container

### Pull the image and start the container

```bash
docker run -e ACCEPT_EULA=Y -e SA_PASSWORD=Test1234! -p 1433:1433 --name sqlserver --hostname sqlserver -d mcr.microsoft.com/mssql/server:2022-latest
```

This pulls the SQL Server 2022 Linux image (about 1.5GB, one-time download) and starts a container named `sqlserver` on port 1433.

### Verify it is running

```bash
docker ps --filter name=sqlserver
docker exec sqlserver /opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P 'Test1234!' -No -Q "SELECT @@VERSION"
```

### Start and stop the container

The container persists between restarts. Docker Desktop must be running first.

```bash
docker start sqlserver    # start after a reboot
docker stop sqlserver     # stop when not in use
```

---

## Build the database

### Create the database

```bash
docker exec sqlserver /opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P 'Test1234!' -No -Q "
IF NOT EXISTS (SELECT 1 FROM sys.databases WHERE name = 'AAD')
    CREATE DATABASE AAD
"
```

### Load your schema

Export your table definitions from SSMS and copy the file into the container, then run it:

```bash
docker cp /path/to/table_export.sql sqlserver:/tmp/table_export.sql
docker exec sqlserver /opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P 'Test1234!' -No -d AAD -i /tmp/table_export.sql
```

FK constraint errors during the import are expected if referenced tables are not in the export. The tables themselves will still be created. You can ignore those errors.

### Create user-defined types

If your database uses UDDTs (common in Infios/HighJump environments), create them before loading any stored procedures:

```sql
CREATE TYPE [dbo].[uddt_output_code] FROM [nvarchar](30) NOT NULL
CREATE TYPE [dbo].[uddt_output_msg]  FROM [nvarchar](1000) NOT NULL
CREATE TYPE [dbo].[uddt_obj_name]    FROM [nvarchar](30) NOT NULL
```

Check the exact definitions in SSMS under: **Databases > [DB] > Programmability > Types > User-Defined Data Types**

### Create DB users

Stored procedures that use `GRANT EXECUTE` will fail to load if the target users do not exist. Create them as login-less users:

```sql
CREATE USER WA_USER WITHOUT LOGIN
CREATE USER AAD_USER WITHOUT LOGIN
```

---

## Load a stored procedure

If the procedure uses `ALTER PROCEDURE`, create a stub first so the ALTER has something to target:

```sql
IF OBJECT_ID('dbo.usp_your_procedure') IS NULL
    EXEC('CREATE PROCEDURE dbo.usp_your_procedure AS RETURN')
```

Then copy your `.sql` file into the container and run it:

```bash
docker cp /path/to/usp_your_procedure.sql sqlserver:/tmp/
docker exec sqlserver /opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P 'Test1234!' -No -d AAD -i /tmp/usp_your_procedure.sql
```

Validate the procedure compiled cleanly against the live schema:

```sql
EXEC sys.sp_refreshsqlmodule 'dbo.usp_your_procedure'
```

---

## The test pattern

All tests follow the same structure: wrap everything in `BEGIN TRAN / ROLLBACK` so the database stays clean after each run.

```sql
BEGIN TRAN

-- 1. Insert reference data
INSERT INTO t_whse (wh_id, description) VALUES ('101', 'Test Warehouse')
INSERT INTO t_location (...) VALUES (...)
INSERT INTO t_item_master (...) VALUES (...)

-- 2. Insert pre-existing data if testing an update path
INSERT INTO t_stored_item (...) VALUES (...)

-- 3. Snapshot before state
SELECT 'BEFORE' AS stage, [columns] FROM [table] WHERE [key]

-- 4. Execute the procedure
DECLARE @code uddt_output_code
DECLARE @msg  uddt_output_msg

EXEC dbo.usp_your_procedure
     @param1 = 'value1'
    ,@param2 = 'value2'
    ,@out_vchCode = @code OUTPUT
    ,@out_vchMsg  = @msg  OUTPUT

SELECT @code AS out_code, @msg AS out_msg

-- 5. Snapshot after state
SELECT 'AFTER' AS stage, [columns] FROM [table] WHERE [key]

ROLLBACK TRAN
PRINT 'Rolled back - test data cleaned up'
```

Ask Claude Code to run the test for you:

```
Run this test against the Docker container and show me the before/after results.
```

---

## Example: testing usp_inventory_adjust

### New inventory (INSERT path)

No existing STO record. Expects a new row to be created with the quantity passed in.

```sql
BEGIN TRAN

INSERT INTO t_whse (wh_id, description) VALUES ('101', 'Test Warehouse')
INSERT INTO t_location (wh_id, location_id, type, status, fifo_date, last_count_date, last_physical_date, user_count, capacity_volume, item_hu_indicator)
VALUES ('101', 'LOC-RECV-01', 'S', 'E', '01/01/1900', GETDATE(), GETDATE(), 0, 0, 'I')
INSERT INTO t_item_master (item_number, wh_id, uom, serial_control, lot_control, inspection_code, reorder_point, reorder_qty, last_count_date, pick_put_id)
VALUES ('ITEM-TEST-001', '101', 'EA', 'N', 'N', 'N', 0, 0, GETDATE(), 'Default')
INSERT INTO t_transaction (system_id, tran_type, description, send_to_adv_link)
VALUES ('WA', '100', 'Test Add Inventory', 'NO')

DECLARE @code uddt_output_code
DECLARE @msg  uddt_output_msg

EXEC dbo.usp_inventory_adjust
     @in_vchWhID            = '101'
    ,@in_vchItemNumber      = 'ITEM-TEST-001'
    ,@in_vchLocationID      = 'LOC-RECV-01'
    ,@in_nType              = 0
    ,@in_vchHUID            = NULL
    ,@in_vchMasterHUID      = NULL
    ,@in_vchLotNumber       = NULL
    ,@in_nStoredAttributeID = NULL
    ,@in_fQty               = 10.0
    ,@in_dtFifoDate         = NULL
    ,@in_dtExpirationDate   = NULL
    ,@in_vchHUType          = NULL
    ,@in_vchShipmentNumber  = NULL
    ,@in_vchReservedFor     = NULL
    ,@in_vchStoStatus       = 'A'
    ,@in_nDestSTOType       = 0
    ,@in_vchEmpID           = 'TESTUSER'
    ,@in_vchProcessType     = NULL
    ,@in_nTransactionCode   = 100
    ,@out_vchCode           = @code OUTPUT
    ,@out_vchMsg            = @msg  OUTPUT

SELECT @code AS out_code, @msg AS out_msg

SELECT sto_id, wh_id, item_number, location_id, actual_qty, status
FROM t_stored_item
WHERE wh_id = '101' AND item_number = 'ITEM-TEST-001'

ROLLBACK TRAN
```

Expected result: `out_code = SUCCESS`, one new row in `t_stored_item` with `actual_qty = 10`.

---

### Update existing inventory (UPDATE path)

Pre-insert an STO record with qty 10, then add 5 more. Expects the same row to be updated, not a new one inserted.

```sql
BEGIN TRAN

INSERT INTO t_whse (wh_id, description) VALUES ('101', 'Test Warehouse')
INSERT INTO t_location (wh_id, location_id, type, status, fifo_date, last_count_date, last_physical_date, user_count, capacity_volume, item_hu_indicator)
VALUES ('101', 'LOC-RECV-01', 'S', 'E', '01/01/1900', GETDATE(), GETDATE(), 0, 0, 'I')
INSERT INTO t_item_master (item_number, wh_id, uom, serial_control, lot_control, inspection_code, reorder_point, reorder_qty, last_count_date, pick_put_id)
VALUES ('ITEM-TEST-001', '101', 'EA', 'N', 'N', 'N', 0, 0, GETDATE(), 'Default')
INSERT INTO t_transaction (system_id, tran_type, description, send_to_adv_link)
VALUES ('WA', '100', 'Test Add Inventory', 'NO')

INSERT INTO t_stored_item (wh_id, item_number, location_id, type, hu_id, actual_qty, unavailable_qty, status, fifo_date, expiration_date, sequence)
VALUES ('101', 'ITEM-TEST-001', 'LOC-RECV-01', 0, NULL, 10.0, 0.0, 'A', GETDATE(), '01/01/1900', 0)

SELECT 'BEFORE' AS stage, sto_id, actual_qty FROM t_stored_item WHERE wh_id = '101' AND item_number = 'ITEM-TEST-001'

DECLARE @code uddt_output_code
DECLARE @msg  uddt_output_msg

EXEC dbo.usp_inventory_adjust
     @in_vchWhID            = '101'
    ,@in_vchItemNumber      = 'ITEM-TEST-001'
    ,@in_vchLocationID      = 'LOC-RECV-01'
    ,@in_nType              = 0
    ,@in_vchHUID            = NULL
    ,@in_vchMasterHUID      = NULL
    ,@in_vchLotNumber       = NULL
    ,@in_nStoredAttributeID = NULL
    ,@in_fQty               = 5.0
    ,@in_dtFifoDate         = NULL
    ,@in_dtExpirationDate   = NULL
    ,@in_vchHUType          = NULL
    ,@in_vchShipmentNumber  = NULL
    ,@in_vchReservedFor     = NULL
    ,@in_vchStoStatus       = 'A'
    ,@in_nDestSTOType       = 0
    ,@in_vchEmpID           = 'TESTUSER'
    ,@in_vchProcessType     = NULL
    ,@in_nTransactionCode   = 100
    ,@out_vchCode           = @code OUTPUT
    ,@out_vchMsg            = @msg  OUTPUT

SELECT @code AS out_code, @msg AS out_msg

SELECT 'AFTER' AS stage, sto_id, actual_qty FROM t_stored_item WHERE wh_id = '101' AND item_number = 'ITEM-TEST-001'

ROLLBACK TRAN
```

Expected result: same `sto_id`, `actual_qty = 15`.

---

## Tips

**Let Claude Code write and run the tests.** Describe the scenario in plain English and Claude will build the test script, execute it against the container, and report the results. You do not need to write the SQL yourself.

**One test per scenario.** Keep each `BEGIN TRAN / ROLLBACK` block focused on a single path through the procedure. Separate tests for insert path, update path, decrement, zero-out, HU scenarios, etc.

**Check `out_code` and `out_msg` first.** A result of `SUCCESS / NONE` means the procedure ran clean. Any error lands in `out_msg` with the `@error_tag` that was set before the failing statement, which tells you exactly where it broke.

**Reference data goes in every test.** Each test is self-contained inside its transaction, so you need to insert the warehouse, location, item, and transaction records every time. This keeps tests independent and runnable in any order.

**The container survives reboots if Docker Desktop is running.** Run `docker start sqlserver` after a reboot before trying to connect. Schema and loaded procedures persist inside the container.

---

## What this looks like end to end

1. Docker Desktop is running, container is up (`docker start sqlserver`)
2. Open Claude Code in your SQL work folder
3. Tell Claude which procedure to test and what scenario to cover
4. Claude writes the test script, copies it to the container, and runs it
5. Claude reads the output and confirms pass or fail
6. Iterate on the procedure or add more test cases as needed

The database stays clean after every test run because every test rolls back. The only thing that persists is the schema and the loaded procedures.
