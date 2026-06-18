# Sample Outputs

Expected AI classifications for a sample of accounts from the master dataset. Use these to validate the live build — if your AI step returns classifications that broadly match these, the logic is working.

Today's reference date: **2026-06-17**

## At-Risk Accounts

**Acme Robotics**
```json
{
  "account_name": "Acme Robotics",
  "signal_type": "at-risk",
  "urgency": "high",
  "reason": "2 open escalations, down sentiment trend, and declining login frequency with renewal on 2026-06-29 — within 30 days.",
  "suggested_action": "CSM should escalate to executive sponsorship this week and schedule an emergency account review before renewal."
}
```

**Fern & Co**
```json
{
  "account_name": "Fern & Co",
  "signal_type": "at-risk",
  "urgency": "high",
  "reason": "1 open escalation, down sentiment, declining logins, renewal on 2026-07-03 — within 30 days.",
  "suggested_action": "CSM should call the account owner this week to surface the escalation and begin renewal conversation."
}
```

## Expansion-Opportunity Accounts

**Lumen Analytics**
```json
{
  "account_name": "Lumen Analytics",
  "signal_type": "expansion-opportunity",
  "urgency": "low",
  "reason": "87% feature adoption, increasing login frequency, up sentiment trend, zero open escalations.",
  "suggested_action": "CSM should schedule a business review to discuss advanced use cases and expansion potential."
}
```

**Stonegate Partners**
```json
{
  "account_name": "Stonegate Partners",
  "signal_type": "expansion-opportunity",
  "urgency": "low",
  "reason": "87% feature adoption, increasing logins, flat sentiment, zero escalations.",
  "suggested_action": "CSM should reach out proactively to discuss expanded seat count or additional modules."
}
```

## Expected distribution across 50 accounts

A realistic weekly run should produce roughly:
- **~8-10 at-risk accounts** routing to immediate email alerts
- **~6-8 expansion-opportunity accounts** routing to growth signal emails
- **~32-36 watch or healthy accounts** landing silently in the Weekly Digest sheet

That ratio is the point. Roughly a quarter of the book needs attention. The rest needs nothing. No human had to read 50 accounts across 3 systems to know that.
