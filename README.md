# Account Risk & Opportunity Digest

AI Fluency exercise submission — Sr. Manager, Global Support role.

## Who this is for

This repo documents a workflow built for the Zapier AI Fluency exercise. It reflects how I actually think about AI in a CS and support organization: not as a drafting tool bolted onto the end of a process, but as a synthesis and decision layer that reduces what has to route through a human.

The data is fully mocked and structured to represent the kind of fragmentation most CS orgs deal with — account data, support activity, and product usage consolidated into a single source for this demo. In production, these signals pull from Salesforce, a support platform (Zendesk/Intercom), and a product analytics tool. The shape of the problem and the logic are drawn from work I'm doing in a real environment at Jobber.

## What it does

Every Monday, the Zap pulls all 50 accounts from a master Google Sheet and loops through each one individually. For each account, an AI step receives 12 signals — support activity, product usage, renewal timing, and account ownership — and classifies the account as `at-risk`, `expansion-opportunity`, `watch`, or `healthy`. It also assigns urgency, writes a one-sentence reason citing specific data points, and recommends a concrete next step for the account owner.

At-risk accounts generate an immediate email alert to the owning CSM. Expansion-opportunity accounts generate a separate alert framed around growth, not risk. Everything else lands quietly in a Weekly Digest sheet. In production both alert paths route to Slack. Email is used here for demo visibility.

## Why this problem

The core issue: a manager can't read every account across every system every week. Risk gets caught late. Growth signals almost never surface. This workflow replaces the manual synthesis step — the cross-referencing across 3-4 systems before judgment can even be applied. The AI makes the classification decision that determines whether a human sees this account at all this week, and if so, with what framing.

## What's in this repo

- `data/` — 50-account master dataset (all 12 signals per account in one sheet)
- `prompts/risk_classification_prompt.md` — the exact prompt used in the AI step, plus design rationale
- `sample_outputs/sample_outputs.md` — expected classifications for a sample of accounts
- `docs/zap_blueprint.md` — step-by-step Zap configuration
- `docs/architecture.md` — flow diagram and design rationale
- `context_doc.md` — written context document (who/what/why)

## Zap structure (5 steps)

1. **Schedule by Zapier** — fires every Monday morning
2. **Google Sheets, Get Many Rows** — pulls all 50 accounts from master sheet
3. **Looping by Zapier** — processes each account individually
4. **AI by Zapier, Custom Prompt** — classifies account, returns structured JSON
5. **Paths by Zapier** — routes to at-risk email, expansion email, or Weekly Digest sheet

## A note on scope

One workflow built with depth rather than several shallow ones. Designed to run unattended, handle the full range of outcomes without generating noise for cases that don't need attention, and produce output a manager can act on without further processing.
