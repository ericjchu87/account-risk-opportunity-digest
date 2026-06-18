# Zap Blueprint: Account Risk & Opportunity Digest

Step-by-step build spec. Replicate in your Zapier account using the master Google Sheet in `/data`.

**Apps used:** Schedule by Zapier, Google Sheets, Looping by Zapier, AI by Zapier, Paths by Zapier, Email by Zapier

---

## Setup — before building

1. Upload `data/Account_Risk_Master_50.xlsx` to Google Drive (opens as Google Sheets)
2. Confirm two tabs: `Account Data` (50 accounts, 12 columns) and `Weekly Digest` (empty with headers)

---

## Step 1: Trigger — Schedule by Zapier

- Trigger event: Every Week
- Day: Monday, Time: 8:00 AM

---

## Step 2: Action — Google Sheets, Get Many Spreadsheet Rows

- Spreadsheet: Account Risk Master 50
- Worksheet: `Account Data`
- Leave all filter fields blank — returns all 50 accounts

---

## Step 3: Action — Looping by Zapier, Create Loop From Line Items

Map all 12 columns from Step 2 as Values 1–12:

| Value | Field |
|---|---|
| Value 1 | account_name |
| Value 2 | segment |
| Value 3 | arr_usd |
| Value 4 | renewal_date |
| Value 5 | csm_owner |
| Value 6 | ticket_volume_30d |
| Value 7 | sentiment_trend |
| Value 8 | open_escalations |
| Value 9 | last_contact_date |
| Value 10 | feature_adoption_pct |
| Value 11 | login_frequency_trend |
| Value 12 | days_since_last_login |

Everything from here runs once per account, per loop iteration.

---

## Step 4: Action — AI by Zapier, Custom Prompt

Paste the full prompt from `prompts/risk_classification_prompt.md`, replacing each bracketed placeholder with the corresponding Value token from the Loop step.

AI by Zapier automatically parses the JSON output into structured fields: `Account Name`, `Signal Type`, `Urgency`, `Reason`, `Suggested Action`.

---

## Step 5: Action — Paths by Zapier

**Path A: At Risk**
- Condition: Signal Type (from Step 4) exactly matches `at-risk`
- Action: Email by Zapier, Send Outbound Email
  - Subject: `[At Risk]` + Account Name token
  - Body: Account name, owner (Value 5), urgency, reason, suggested action
  - Note: routes to Slack in production

**Path B: Expansion Opportunity**
- Condition: Signal Type exactly matches `expansion-opportunity`
- Action: Email by Zapier
  - Subject: `[Growth Signal]` + Account Name token
  - Body: Account name, owner, reason, suggested action
  - Note: routes to separate Slack channel in production

**Path C: Watch or Healthy**
- Condition: Signal Type does not match `at-risk` AND does not match `expansion-opportunity`
- Action: Google Sheets, Create Spreadsheet Row
  - Worksheet: `Weekly Digest`
  - Map: account_name, signal_type, urgency, reason, suggested_action from AI step; week_of from Schedule trigger

---

## Testing note

Zapier test mode processes one account at a time. The full loop across all 50 accounts only fires on a live scheduled run. Use Acme Robotics data hardcoded in the AI prompt to validate at-risk classification, and Lumen Analytics data to validate expansion-opportunity, before going live.

---

## A note on testing safely

While iterating on the AI step and Paths logic, point Path A and Path B at your own email. Once the logic checks out, switch recipients to the intended audience for the final demo run.
