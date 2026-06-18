# Risk & Opportunity Classification Prompt

This is the exact prompt used in the **AI by Zapier** step (Custom Prompt). It receives one account's combined data and returns a structured JSON classification that the Paths step branches on. The model is making a decision — not drafting text — and that decision is the "real work in the logic" the build is designed around.

## Prompt

```
You are a Customer Success signal analyst for a SaaS company. Today's date is 2026-06-17. Classify the following account based strictly on the rules below and return ONLY a valid JSON object. No preamble, no explanation, no markdown, no backticks, no text outside the JSON.

ACCOUNT DATA:
- Account: [Value 1 from Loop]
- Segment: [Value 2 from Loop]
- ARR (USD): [Value 3 from Loop]
- Renewal date: [Value 4 from Loop]
- CSM owner: [Value 5 from Loop]
- Tickets last 30 days: [Value 6 from Loop]
- Sentiment trend: [Value 7 from Loop]
- Open escalations: [Value 8 from Loop]
- Last contact date: [Value 9 from Loop]
- Feature adoption %: [Value 10 from Loop]
- Login frequency trend: [Value 11 from Loop]
- Days since last login: [Value 12 from Loop]

CLASSIFICATION RULES — apply in this exact order, stop at the first match:
1. at-risk: EITHER (open_escalations > 0 AND sentiment_trend = down AND login_frequency_trend = declining) OR (open_escalations > 0 AND (sentiment_trend = down OR login_frequency_trend = declining) AND renewal is within 60 days of today).
2. expansion-opportunity: feature_adoption_pct >= 80 AND login_frequency_trend = increasing AND sentiment_trend is not down AND open_escalations = 0.
3. watch: any single negative signal present that does not meet the at-risk threshold above.
4. healthy: no negative signals and expansion threshold not met.

URGENCY RULES:
- at-risk + renewal within 30 days = high
- at-risk + renewal beyond 30 days = medium
- expansion-opportunity + renewal within 90 days = medium
- everything else = low

REQUIRED OUTPUT — return exactly this structure, no deviations:
{"account_name":"","signal_type":"at-risk|expansion-opportunity|watch|healthy","urgency":"high|medium|low","reason":"One sentence citing specific data points","suggested_action":"One specific concrete action for the CSM owner this week."}
```

## Why this design

**Explicit ordered rules** rather than "use your judgment" keep the output consistent across runs and make the logic auditable. If a CSM asks why an account was flagged, the `reason` field plus the rule it matched is a real answer.

**Structured JSON output** is what makes this a logic step rather than a drafting step. The Paths step downstream reads `signal_type` directly to decide where the account goes.

**Reasoning and suggested_action are still generated freely** within the structure — this is where the AI adds judgment on top of the rules, rather than just following a flowchart a human could have coded with if/else.

**Stop at first match** prevents the model from trying to apply multiple rules to edge cases and keeps output consistent across 50 accounts run in sequence.

**Today's date is hardcoded** so the model can calculate renewal windows accurately for the 30/60/90-day urgency rules. In production this would be injected dynamically via a Formatter step.

**Expansion and at-risk route separately** because a churn risk and a growth signal are different conversations that should never feel the same. Different framing, different urgency, different people.
