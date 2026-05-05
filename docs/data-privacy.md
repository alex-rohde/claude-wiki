# Data Privacy

!!! info "Keep this page up to date"
    Anthropic's policies may change. Always check the official sources for the most current information:

    - Privacy policy: [anthropic.com/privacy](https://www.anthropic.com/privacy)
    - Enterprise security: [claude.com/pricing/enterprise](https://claude.com/pricing/enterprise)
    - Trust Center: [anthropic.com/trust](https://www.anthropic.com/trust)

---

## Does Anthropic train on your data?

The answer depends on your plan.

| Plan | Used for model training? |
|---|---|
| **Free** | Yes, by default. Can be opted out in account settings. |
| **Pro** | Yes, by default. Can be opted out in account settings. |
| **Team / Enterprise** | **No.** Anthropic explicitly does not use inputs or outputs to train models for Team and Enterprise customers. |

!!! warning "Safety exception"
    Even with opt-out enabled on Free/Pro, Anthropic may use data flagged for safety review or submitted through explicit feedback mechanisms.

If you're unsure which plan your team is on, ask your manager or the person who provisioned your account.

---

## What data does Anthropic collect?

When you use any Claude product, Anthropic collects:

- **Prompts and outputs** — the content of your conversations
- **Identity data** — name, email address
- **Technical data** — device type, IP address, browser info, usage patterns, error logs
- **Payment information** — for paid plans

This applies to Claude.ai web, Claude Code, and Claude Cowork. When you paste code or describe a problem, that content is sent to Anthropic's servers to generate a response.

**Practical implication for developers:** avoid pasting secrets, credentials, or highly sensitive proprietary code into any Claude interface. Treat it the same way you'd treat any cloud-based tool.

---

## Data retention

Conversations are automatically deleted from Anthropic's back-end within **30 days**. You can also manually delete individual conversations at any time from your account.

Enterprise customers have access to **custom data retention controls** to align with their organization's policies.

---

## Security (Enterprise/Team)

For Team and Enterprise accounts, Anthropic provides:

- **SOC 2 Type II** compliance
- **AES-256 encryption** at rest and in transit
- **HIPAA-ready** offering for regulated industries
- SSO and domain capture
- SCIM for automated user provisioning
- Audit logs and role-based access controls

---

## Data Processing Agreement (DPA)

If your company is on a Team or Enterprise plan, Anthropic offers a Data Processing Agreement. Check with your account admin — a DPA may already be in place. If not, contact your Anthropic account representative.

---

## Questions?

Talk to your manager or IT/security team if you have questions about how Claude is approved for use at your company, or what data handling agreements are in place.
