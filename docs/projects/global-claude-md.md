# Global CLAUDE.md Setup

A global `CLAUDE.md` lives in your home directory (`~/CLAUDE.md`) and loads automatically for **every** Claude Code session, regardless of which project folder you start from. Use it for standards and templates that apply across all accounts  **not for anything customer or database specific.**

---

## What belongs in the global file

| Include | Exclude |
|---|---|
| Coding conventions (naming prefixes, style rules) | Customer names or database names |
| Stored procedure template | Schema or table references |
| SQL formatting examples | Environment-specific config |
| General patterns you use everywhere | Anything that varies by account |

---

## Location

```
~/CLAUDE.md          ← global, loads for every session
~/eplus/CLAUDE.md    ← project-level, loads only from that folder
```

The global file and the project-level file both load when you start Claude Code from a project folder — they stack. Global conventions apply everywhere; project context fills in the specifics.

---

## What we put in ours

- Coding Conventions
- Stored Procedure Template
- SQL Formatting Examples

---

## How to set it up

1. Open a terminal and run:

    ```bash
    touch ~/CLAUDE.md
    ```

2. Copy the full template below into `~/CLAUDE.md`.

3. Start Claude Code from any project folder — the global file loads automatically.

!!! tip
    Keep the global file to standards and templates only. The moment you add a table name or database name, it starts bleeding into every other account's sessions.

---

## Full Global CLAUDE.md

```markdown
# Global — SQL Development Standards

## Coding Conventions

- Stored procedures: `usp_` prefix
- Temp tables: `#tmp_` prefix
- Always include `SET NOCOUNT ON`
- Use `TRY/CATCH` for error handling
- No `SELECT *` in production code

---

## Stored Procedure Template

```sql
USE AAD
GO

IF EXISTS (SELECT 1 FROM sysobjects WHERE name = 'usp_ADp1234_page_title_template_for_ww')
BEGIN
    PRINT ''
    PRINT 'Dropping old version of [usp_ADp1234_page_title_template_for_ww]'
    DROP PROCEDURE [dbo].[usp_ADp1234_page_title_template_for_ww]
END
GO

PRINT ''
PRINT 'Creating Stored Procedure usp_ADp1234_page_title_template_for_ww'
GO

CREATE PROCEDURE [dbo].[usp_ADp1234_page_title_template_for_ww]
     @in_ww_username        NVARCHAR(10)
    ,@in_wh_id              NVARCHAR(10)
    ,@in_location_id        INT
    ,@in_start_date         VARCHAR(30)
    ,@in_end_date           VARCHAR(30)
    ,@in_tran_start         VARCHAR(30) -- passed in from search page to capturing when they started the tran   
--
    ,@in_debug              VARCHAR(3) = 'NO' -- Pass in 'YES' to return results to SSMS
    ,@out_msg               VARCHAR(250) = NULL OUTPUT
AS

/* ================================================================================================
DESCRIPTION:
    This (put the description of what this sproc does here).

HISTORY:
    Date          Author                Description
--     MM/DD/YYYY    Your Name           KT-40 Created

HELPERS:
    GRANT EXECUTE ON usp_ADp1234_page_title_template_for_ww TO AAD_USER, WA_USER;

    Run the following, update the results and wrap with BEGIN TRAN and ROLLBACK for testing:
    usp_print_sproc usp_ADp1234_page_title_template_for_ww

================================================================================================ */
SET NOCOUNT ON;

-- START OF COMMON VARIABLES, STANDARD ACROSS SPROCS 
DECLARE
    @c_obj_name          VARCHAR(100) = OBJECT_NAME(@@PROCID),
    @c_called_from       NVARCHAR(250) = 'Config Name - Page Title',
    @error_number        INT,
    @error_severity      INT,
    @error_state         INT,
    @error_line          INT,
    @error_message       NVARCHAR(MAX),
    @program_name        NVARCHAR(MAX),
    @call_string         NVARCHAR(MAX),
    @error_tag           NVARCHAR(100),
    @deadlock_retry      TINYINT = 0, -- retry 3 times for a total of 4 attempts
    @starting_tran_count TINYINT,
    @internal_tran       VARCHAR(3),
    @c_log_level         TINYINT,
    @row_count           INT
    DECLARE @t_buffer table ( EventType NVARCHAR(30), Parms INT, EventInfo NVARCHAR(MAX) )
-- END OF COMMON

-- LOCAL VARIABLES
DECLARE 
     @hu_id                 NVARCHAR(30)

-- INITIALIZE SPROC VARIABLES
SET @starting_tran_count = @@TRANCOUNT

START:
BEGIN TRY

 -- INITIALIZE TRY VARIABLES and all TABLE VARIABLES not inside the transaction
    SET @internal_tran = N'NO';

    -----------------------------------------------------------------------------------
    --  Set Log Level and Log Start with Input Parameters
    -----------------------------------------------------------------------------------

    -- Grab the Log Level and insert it if it doens't exsit (if @in_wh_id doesn't exist, you can remove it)
    SELECT @c_log_level = log_level FROM t_log_control WITH(NOLOCK) WHERE wh_id = @in_wh_id AND source = @c_obj_name
    IF @@ROWCOUNT = 0
    BEGIN
        SET @c_log_level = 0
        INSERT INTO t_log_control (wh_id, source, log_level) VALUES (@in_wh_id, @c_obj_name, @c_log_level)
    END

    IF @in_debug = N'YES'
        BEGIN
            UPDATE t_log_control 
               SET log_level = 2 
             WHERE wh_id = @in_wh_id 
               AND source = @c_obj_name 
               AND log_level <> 2
          END
    ELSE IF @in_debug = N'NO'
        BEGIN
            UPDATE t_log_control 
               SET log_level = 0 
             WHERE wh_id = @in_wh_id 
               AND source = @c_obj_name 
               AND log_level <> 0
          END
 
    -- LOG --
    IF @c_log_level >= 1 -- 1 = Info, 2 = Debug
        BEGIN
            INSERT INTO t_log (wh_id, source, user_id, message_text)
            SELECT @in_wh_id, @c_obj_name, @in_user_id, EventInfo
              FROM @t_buffer 
          END
    -- LOG --

    ------------------------------------------------------------------------------------
    -- Section Title
    ------------------------------------------------------------------------------------

    -- Non-transactional sproc logic here

    ------------------------------------------------------------------------------------
    -- Example Code
    ------------------------------------------------------------------------------------

    -- The following are examples of how and why to use a temporary table
    --      1. not have to hit the database table again
    --      2. allows you to maintain a single statement grabbing data and filling variables
    --      3. easily debug by writing the results to the t_log table during development/runtime (see below).
    
    -- How to use @error_tag
    --      1. Be sure to set a unique @error_tag before each statement
    --      2. This way when something fails this @error_tag will show up in t_error_log
    --      3. You can then search for the text and know exactly the statement in error

    SET @error_tag = 'fill #tmp_sto_on_fork'

    SELECT wh_id, location_id, hu_id, item_number, lot_number, actual_qty, status, type
    INTO #tmp_sto_on_fork
    FROM t_stored_item WITH (NOLOCK)
    WHERE wh_id = @in_wh_id
        AND location_id = @in_location_id
        
    -- Now that you have a table in memory you can do a ton without the the data changing or having to hit the database again.

    -- Example of setting variables to use if/when needed and only having to maintain the statement above
    SET @error_tag = 'set variables from #tmp_sto_on_fork'

    SELECT TOP 1
         @hu_id = hu_id
    FROM #tmp_sto_on_fork

    -- Logging debug results this way speeds up development AND you can turn back on in production to troubleshoot an issue.
    -- LOG --
    IF @c_log_level >= 2 -- 1 = Info, 2 = Debug
        INSERT INTO t_log (wh_id, source, user_id, message_text)
            SELECT @in_wh_id, @c_obj_name, @in_ww_username,
                '#tmp_sto_on_fork: '
                + 'wh_id [' + wh_id + ']'
                + ', location_id [' + location_id + ']'
                + ', hu_id [' + ISNULL(hu_id, '(NULL)') + ']'
                + ', item_number [' + item_number + ']'
                + ', lot_number [' + ISNULL(lot_number, '(NULL)') + ']'
                + ', actual_qty [' + CAST(actual_qty AS VARCHAR) + ']'
                + ', status [' + status + ']'
                + ', type [' + type + ']'
            FROM #tmp_sto_on_fork
    -- LOG --
    
    -- DEV NOTES:
    -- The following code to the CATCH block is the standard we use for handling code inside transaction.
    -- If you don't have any code that belongs in a transaction, or you don't need to record a tran_log record,
    -- delete everything from the BEGIN to the END TRAN comment.

    ----------------
    -- BEGIN TRAN --
    ----------------

    SET @error_tag = 'starting tran';

    IF @starting_tran_count = 0
    BEGIN
        SET @internal_tran = 'YES'
        BEGIN TRAN
    END

        ------------------------------------------------------------------------------------
        -- Section Title
        ------------------------------------------------------------------------------------

        -- Transactional sproc logic here.

        /* uncomment the following when a transaction is needed.

        ------------------------------------------------------------------------------------
        -- Generate Transaction
        ------------------------------------------------------------------------------------
    
        SET @error_tag = 'get tran desc'

        DECLARE
             @tran_type             NVARCHAR(3) = '999'
            ,@tran_desc             NVARCHAR(50)

        -- Get Transaction Description
        SELECT @tran_desc = description FROM t_transaction WITH (NOLOCK) WHERE system_id = 'WA' AND tran_type = @tran_type 
    
        IF @tran_desc IS NULL
            SET @tran_desc = 'Undefined in t_transaction'

        SELECT @tran_end = GETDATE()

        SET @error_tag = 'insert t_tran_log_holding'

        -- Insert into the holding table and the background process in WA PROCESSORS will move it to t_tran_log
        INSERT INTO t_tran_log_holding
            (tran_type
            ,description
            ,start_tran_date
            ,start_tran_time
            ,end_tran_date
            ,end_tran_time
            ,employee_id
            ,control_number
            ,line_number
            ,contusp_aaa_function_template_for_visionrol_number_2
            ,outside_id
            ,wh_id
            ,location_id
            ,hu_id
            ,num_items
            ,item_number
            ,lot_number
            ,uom
            ,tran_qty
            ,wh_id_2
            ,location_id_2
            ,verify_status
            ,employee_id_2
            ,routing_code
            ,hu_id_2
            ,elapsed_time
            ,sys_device
            ,calling_procedure
            ,menu_process
            )
        SELECT 
             @tran_type      --tran_type
            ,@tran_desc      --description
            ,CONVERT(DATE, @in_tran_start)          --start_tran_date
            ,CONVERT(TIME, @in_tran_start)          --start_tran_time
            ,CONVERT(DATE, @tran_end)               --end_tran_date
            ,CONVERT(TIME, @tran_end)               --end_tran_time
            ,@in_ww_username    --employee_id
            ,NULL               --control_number
            ,0                  --line_number
            ,NULL               --control_number_2
            ,NULL               --outside_id
            ,@in_wh_id          --wh_id (source)
            ,NULL               --location_id (source)
            ,NULL               --hu_id (source)
            ,NULL               --num_items
            ,NULL               --item_number
            ,NULL               --lot_number
            ,NULL               --UOM
            ,0                  --tran_qty
            ,NULL               --wh_id_2 (dest)
            ,NULL               --location_id_2 (dest)
            ,NULL               --verify_status
            ,NULL               --employee_id_2
            ,NULL               --routing_code
            ,NULL               --hu_id_2 (dest)
            ,DATEDIFF(ss, @in_tran_start, @tran_end) AS elapsed     --elapsed_time
            ,NULL    --sys_device
            ,@c_obj_name        --calling_procedure
            ,c_called_from      --menu_process
        */

        SET @row_count = @@ROWCOUNT

        IF @in_debug = 'YES'
            SELECT * FROM t_tran_log_holding WITH (NOLOCK) WHERE tran_log_holding_id = @@IDENTITY

    IF @starting_tran_count = 0
        COMMIT TRAN
    
    ----------------
    --  END TRAN  --
    ----------------

    SET @out_msg = ' Tran Log records inserted [' + CAST(ISNULL(@row_count,0) AS VARCHAR) + '].'

END TRY
BEGIN CATCH

    IF @starting_tran_count = 0 AND @internal_tran = 'YES'
    ROLLBACK TRAN

    SELECT @program_name = program_name FROM sys.dm_exec_sessions WHERE Session_id = @@SPID

    INSERT INTO @t_buffer ( EventType, Parms, EventInfo )
    EXEC ('DBCC INPUTBUFFER(@@SPID)')

    SELECT @call_string = EventInfo FROM @t_buffer

    SELECT
        @error_number    = ERROR_NUMBER(),
        @error_severity  = ERROR_SEVERITY(),
        @error_state     = ERROR_STATE(),
        @error_line      = ERROR_LINE(),
        @error_message = 'Sproc @error_tag = [' + @error_tag + '], ERROR_MESSAGE() = ' + ERROR_MESSAGE()

    INSERT INTO t_error_log
    (
        error_number,
        error_severity,
        error_state,
        error_procedure,
        error_line,
        error_message,
        username,
        type,
        call_string,
        called_from
    )
    SELECT
        @error_number,
        @error_severity,
        @error_state,
        @c_obj_name,
        @error_line,
        @error_message,
        @in_ww_username,
        'WEBWISE', -- ARCHITECT, WEBWISE, JOB, VISION
        @call_string,
        @c_called_from

    -- Set @out_msg
    SET @out_msg = 'IN CATCH BLOCK: The last @error_tag set = [' + @error_tag + '], t_error_log.error_log_id = [' + CAST(@@IDENTITY AS VARCHAR) + ']'
    IF @error_number = 1205 -- add the deadlock counter
        SET @out_msg = @out_msg + ', DEADLOCKED with @deadlock_retry = [' + CAST(@deadlock_retry AS VARCHAR) + ']'

    -- LOG --
    IF @c_log_level >= 1 -- 1 = Info, 2 = Debug
        INSERT INTO t_log (wh_id, source, user_id, message_text)
            SELECT @in_wh_id, @c_obj_name, @in_ww_username,
                '@out_msg = [' + @out_msg + ']'
    -- LOG --

    IF @error_number = 1205
    BEGIN
        IF @deadlock_retry < 3
        BEGIN
            -- WAITFOR DELAY '00:00:01'
            SET @deadlock_retry = @deadlock_retry + 1
            GOTO START
        END
    END

END CATCH

------------------ Return to calling process ----------------------
EXIT_LABEL:
    RETURN
GO

PRINT ''
PRINT 'Stored Procedure created'
GO

GRANT EXECUTE ON dbo.usp_ADp1234_page_title_template_for_ww TO WA_USER, AAD_USER
GO

PRINT ''
PRINT 'EXECUTE has been granted to WA_USER, AA_USER'
GO
```

---

## SQL Formatting Examples

### SELECT with HAVING

```sql
SELECT TOP 100 COUNT(pick_id)
              ,order_number
              ,status
          FROM t_pick_detail WITH(NOLOCK)
         WHERE status = N'RELEASED'
      GROUP BY order_number, status
        HAVING COUNT(pick_id) > 1
```

---

### UPDATE

```sql
UPDATE t_fwd_pick
   SET wh_id                     = N'101'
      ,location_id               = N'LOC-01'
      ,item_number               = N'ITEM_NUM'
      ,uom                       = N'EA'
      ,tolerance_percent         = N'20'
      ,minimum_trigger_qty       = N'20'
      ,maximum_replenishment_qty = N'100'
      ,default_priority          = N'10'
      ,calculate_qty_flag        = N'N'
      ,stored_attribute_id       = NULL
 WHERE location_id               = N'LOC-01'
```

---

### UPDATE with JOIN

```sql
UPDATE fwd
   SET wh_id                     = N'101'
      ,location_id               = N'LOC-01'
      ,item_number               = N'ITEM_NUM'
      ,uom                       = N'EA'
      ,tolerance_percent         = N'20'
      ,minimum_trigger_qty       = N'20'
      ,maximum_replenishment_qty = N'100'
      ,default_priority          = N'10'
      ,calculate_qty_flag        = N'N'
      ,stored_attribute_id       = NULL
  FROM t_fwd_pick fwd
  JOIN t_location loc WITH(NOLOCK)
    ON loc.location_id = fwd.location_id
   AND loc.wh_id       = fwd.wh_id
 WHERE fwd.location_id = N'LOC-01'
```

---

### DELETE

```sql
DELETE FROM t_fwd_pick
      WHERE location_id = N'LOC-01'
```

---

### DELETE with JOIN

```sql
DELETE fwd
  FROM t_fwd_pick fwd
  JOIN t_location loc
    ON fwd.location_id = loc.location_id
   AND fwd.wh_id       = loc.wh_id
 WHERE fwd.location_id = N'LOC-01'
```

---

### SELECT INTO

```sql
SELECT wh_id
      ,location_id
      ,item_number
      ,uom
      ,tolerance_percent
      ,minimum_trigger_qty
      ,maximum_replenishment_qty
      ,default_priority
      ,calculate_qty_flag
      ,stored_attribute_id
  INTO #tmp_fwd_pick
  FROM t_fwd_pick
 WHERE wh_id = N'101'
```

---

### INSERT

```sql
INSERT INTO t_fwd_pick
(
     wh_id
    ,location_id
    ,item_number
    ,uom
    ,tolerance_percent
    ,minimum_trigger_qty
    ,maximum_replenishment_qty
    ,default_priority
    ,calculate_qty_flag
    ,stored_attribute_id
)
VALUES
(
     N'101'
    ,N'LOC-01'
    ,N'ITEM_NUM'
    ,N'EA'
    ,N'20'
    ,N'20'
    ,N'100'
    ,N'10'
    ,N'N'
    ,NULL
)
```

---

### DECLARE / SET

```sql
DECLARE @wh_id          NVARCHAR(10)
DECLARE @location_id    NVARCHAR(20)
DECLARE @item_number    NVARCHAR(50)
DECLARE @uom            NVARCHAR(10)

SET @wh_id          = N'101'
SET @location_id    = N'LOC-01'
SET @item_number    = N'ITEM_NUM'
SET @uom            = N'EA'
```

---

### SELECT with JOINs and GROUP BY

```sql
SELECT   loc.wh_id
        ,loc.location_id
        ,loc.location_type
        ,itm.item_number
        ,itm.description
        ,COUNT(pk.pick_id)  AS pick_count
        ,SUM(pk.qty)        AS total_qty
    FROM t_pick_detail   pk  WITH(NOLOCK)
    JOIN t_location      loc WITH(NOLOCK) ON loc.location_id = pk.location_id
                                         AND loc.wh_id       = pk.wh_id
    JOIN t_item_master   itm WITH(NOLOCK) ON itm.item_number = pk.item_number
   WHERE pk.status    = N'RELEASED'
     AND loc.wh_id    = N'101'
GROUP BY loc.wh_id
        ,loc.location_id
        ,loc.location_type
        ,itm.item_number
        ,itm.description
ORDER BY loc.location_id
```

---

### Temp Table CREATE / DROP

```sql
IF OBJECT_ID('tempdb..#tmp_fwd_pick') IS NOT NULL DROP TABLE #tmp_fwd_pick

CREATE TABLE #tmp_fwd_pick
(
     wh_id                      NVARCHAR(10)
    ,location_id                NVARCHAR(20)
    ,item_number                NVARCHAR(50)
    ,uom                        NVARCHAR(10)
    ,tolerance_percent          NVARCHAR(10)
    ,minimum_trigger_qty        NVARCHAR(10)
    ,maximum_replenishment_qty  NVARCHAR(10)
    ,default_priority           NVARCHAR(10)
    ,calculate_qty_flag         NVARCHAR(1)
    ,stored_attribute_id        NVARCHAR(20)
)
```

---

### WHILE Loop

```sql
DECLARE @counter    INT = 1
DECLARE @max        INT

SELECT @max = COUNT(*) FROM #tmp_fwd_pick

WHILE @counter <= @max
BEGIN

    -- logic here

    SET @counter = @counter + 1

END
```

---

### Cursor

```sql
DECLARE @wh_id          NVARCHAR(10)
DECLARE @location_id    NVARCHAR(20)

DECLARE cur_locations CURSOR FOR
    SELECT wh_id
          ,location_id
      FROM #tmp_fwd_pick

OPEN cur_locations

FETCH NEXT FROM cur_locations INTO @wh_id, @location_id

WHILE @@FETCH_STATUS = 0
BEGIN

    -- logic here

    FETCH NEXT FROM cur_locations INTO @wh_id, @location_id

END

CLOSE cur_locations
DEALLOCATE cur_locations
```

---

### EXECUTE

```sql
DECLARE @return_value INT

EXEC @return_value = [dbo].[usp_ProcedureName]
     @Param1    = N'Value1'
    ,@Param2    = N'Value2'

SELECT @return_value AS ReturnValue
```

---

### EXECUTE from Advantage Architect

```sql
STATEMENT = usp_ProcedureName
RETURNS   = return_value

@Param1   = Value1
@Param2   = Value2
```

---

### UNION ALL

```sql
SELECT wh_id
      ,location_id
      ,item_number
      ,status
  FROM t_pick_detail WITH(NOLOCK)
 WHERE status = N'RELEASED'

UNION ALL

SELECT wh_id
      ,location_id
      ,item_number
      ,status
  FROM t_pick_detail WITH(NOLOCK)
 WHERE status = N'COMPLETE'
```
```
