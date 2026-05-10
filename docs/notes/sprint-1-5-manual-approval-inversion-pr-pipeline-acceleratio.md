---
title: Sprint 1.5: Manual Approval Inversion & PR Pipeline Acceleration
updated: 2026-05-09
sources:
  - https://github.com/Soilytix/soilytix-revenue-automation/commit/c41e70a
  - https://github.com/Soilytix/soilytix-revenue-automation/blob/main/derive_icp_metrics.py
tags: [pr-pipeline, automation-default-inversion, bd-foundation, gtm-engineering]
---

# Sprint 1.5: Manual Approval Inversion & PR Pipeline Acceleration

**What.** In seven days, we moved a publication discovery pipeline from 4-week ramp to 1-week ramp by inverting one architectural assumption: defaulting to *approve*, not *reject*, and making human triage an exception, not a bottleneck. The result: 12 active publications live, 93% real funnel conversion (vs. 43% assumed), and the first batch of 5 emails/day shipped Monday instead of June 1.

**Why this matters.** Most revenue automation teams build pipelines that require human sign-off at every stage. That design is not "careful." It is a velocity tax. When approval is the critical path, automation dies. This sprint documents the pattern inversion that killed that tax at Soilytix.

## Why It Matters

Automation pipelines in B2B GTM live or die on two metrics: throughput and human drag. A 50-minute/week manual approval gate does not sound catastrophic until you measure what it costs.

Before Sprint 1.5:
- 6 active publications, 8 stuck in queue
- 43% conversion assumed (never measured real)
- Canary approval manual: 50 minutes weekly
- Week 4 target date for 5 emails/day
- Manual triage was the default; automation was permission-based

After Sprint 1.5:
- 12 active publications, 1 stuck (edge case)
- 93% conversion measured (real funnel data)
- Auto-approve grace window: 4 hours, canary to production
- Week 1 target hit Monday May 11
- Rejection is the default; approval is permission-based

The architectural shift: inverting the approval burden from "assume human judgment" to "assume machine competence, human override." This is not theoretical. The commit diff shows it: nine commits across one Saturday (747f2b7 → 77368e9 → 5b72f11) collapsed three weeks of assumed work into execution.

## The Principle: Default to Automate, Not to Guard

Most teams inherit a legacy mental model: *automation needs permission*. The inverse is more efficient for low-volume, high-relationship B2B GTM: *human review needs justification*.

The three moving parts:

### 1. Measure Real Conversion Before Scaling

We assumed 43% of Perplexity research would surface valid contact email. We never tested it against live publication data. Week 1 Sprint 1.5, we instrumented the pipeline to capture actual conversion by source, email validity, and retry stage.

Result: 93% real conversion on D+7 retry (3-attempt cascade with attempt-aware prompts: generic → LinkedIn-first → imprint-focused). The "43%" was not wrong; it was incomplete. It did not account for retry depth.

This changes scaling math. If you assume 43%, you budget conservatively and approve slowly. If you measure 93%, you can approve fast and let rejection be the edge case.

### 2. Invert the Approval Gate

Old flow: Discover → Manual triage → Retry → Manual approve → Send.
New flow: Discover → Auto-approve (4h grace) → Manual reject (if stuck) → Retry → Send.

The `cmd_canary_auto_approve` cron (06:15 UTC weekdays) auto-approves any canary pub with valid email, no human intervention. A human can still reject it. But rejection is now *deviation*, not *default*.

Code protection: `_eligible_for_retry` skips any publication already flagged with valid email in prior manual triage. This prevents double-approvals and respects the human decisions already made.

Result: manual approval bottleneck dropped from 50 min/week to zero on the happy path. Stuck publications no longer block the funnel. BD V2 unblocked for Sprint 2.

### 3. Human + AI Joint Triage at Inflection Points

Where automation *should* require human review: high-touch manual sourcing and edge-case validation.

Cleiton (human) reviewed 7 publications by hand. Claude (via Perplexity Sonar Pro) reviewed 1 (Geert Hekkert, Hoofdredacteur, 5-source confidence). This is not "humans vs. AI." It is "humans at inflection points + AI for scale."

The split: manual triage on high-context targets (publications with institutional relationships or custom angle). AI triage on volume targets (generic sector research).

Outcome: 12 active publications. Funnel conversion from 43% assumed to 93% measured. No bottleneck.

## The Build: Three Wins in One Sprint

**Win 1: Batch Discovery Acceleration.**
Changed `discover --batch=1` to `discover --batch=3`. Raw publication inputs went from 3/day to 5/day (+66%). This is not feature work. This is parameter tuning once you know conversion is real.

**Win 2: D+7 Retry with Attempt-Aware Prompts.**
After 3 days of no response, retry with Perplexity research. Not with the same prompt. Three attempts:
1. Generic sector angle ("Your publication covers [sector]. [Company] is entering that market.")
2. LinkedIn-first deep-dive (find founder + recent posts on adjacent topic)
3. Imprint-focused (CEO name, recent funding, regulatory shifts in their coverage)

Each attempt is a separate Perplexity call. Cost: ~$0.30/pub/retry. Conversion lift: 43% → 93% cumulative.

**Win 3: Weekly Digest, Slack Automation.**
Every weekday 16:30 UTC, `cmd_stuck_digest` fires a Slack ping with stuck publications and recommended retry strategy. This is not a report. This is a call to action. No stuck pub sits longer than 2 days without visibility.

## The Code: Seven Sections, One Structure

README depth-match: the operational document scaled from 706 lines to 1394 lines. Each of the seven pipeline sections (discover, triage, retry, approve, send, monitor, feedback) now mirrors the same structure:
- Input/output contract
- Success criteria + instrumentation
- Known edge cases + overrides
- Example invocation
- Common errors

This is not bloat. It is onboarding tax paid once so future contributors do not re-learn the same gotchas.

## ICP Quadrants: Where This Acceleration Matters Most

Not all publications are equal. The `derive_icp_metrics.py` cron (weekly) populates two Airtable tabs:

**Sweet Spots (University):** Low institutional friction, high decision-velocity. 5-person team. Example: ag extension directors, university department heads. Auto-approve cadence: fast. Retry depth: 2-3. Expected conversion: 80%+.

**Hard Won (Forestry/Conservation):** High relationship leverage, long sales cycle. 8-person team, requires founder or VP touch. Auto-approve cadence: slow. Retry depth: 5. Expected conversion: 60%.

**Volume Play (Farming/Biotech):** High publication count, moderate conversion. 3-person team per pub. Auto-approve cadence: aggressive. Retry depth: 1. Expected conversion: 70%.

The quadrant data drives approval strategy, retry depth, and team assignment. This is instrumentation *at the level of customer segment*, not just pipeline.

## Tradeoffs & When Not to Use This Pattern

Default-to-automate works in these conditions:
1. **Conversion is measurable.** If your action depends on subjective human judgment (tone, brand fit, cultural alignment), auto-approval is premature. Measure real conversion first.
2. **Volume is sufficient.** If you send <5 emails/day, the 50-min/week approval cost is not the bottleneck. Fix discovery or content first.
3. **Failure is recoverable.** If auto-approving a bad publication damages a relationship permanently, keep humans in the loop. For cold outreach, a bounce is not a relationship risk.
4. **Retry logic exists.** If you cannot improve a prospect contact on D+7, do not auto-retry. You will just spam faster.

When NOT to invert:
- High-touch enterprise accounts (approval should be human)
- Regulated outreach (healthcare, finance) where compliance requires audit
- Founder-to-founder outreach (approval should be founder)
- Campaigns where brand reputation is the product (media, fintech)

This pattern is purpose-built for B2B SaaS cold outreach at scale. It is not universal.

## Connections

[[wiki/sprint-foundations-1-icp-instrumentation]] — The ICP quadrant system that gates approval strategy.

[[wiki/perplexity-retry-depth-economics]] — D+7 retry cost model and attempt-aware prompt design.

[[wiki/manual-triage-as-inflection]] — When human review is worth the friction (vs. when to automate it away).

[[wiki/slack-digest-as-forcing-function]] — Using async notifications to surface stuck states without synchronous meetings.

[[wiki/readme-depth-match-pattern]] — Structure-matching to reduce contributor ramp time.

## Sources

[1] Soilytix revenue-automation repository: https://github.com/Soilytix/soilytix-revenue-automation/commit/c41e70a

[2] derive_icp_metrics.py weekly cron and ICP quadrant schema: https://github.com/Soilytix/soilytix-revenue-automation/blob/main/derive_icp_metrics.py

[3] cmd_stuck_digest Slack automation: Soilytix internal pipeline monitoring.

[4] cmd_canary_auto_approve grace window logic: Soilytix GHA workflow (06:15 UTC cron, `_eligible_for_retry` protection).

[5] Perplexity Sonar Pro attempt-aware retry prompts: Soilytix D+7 retry research protocol.

---

## Next: Operationalize This Pattern in Your Pipeline

If this pattern maps to your GTM automation, the next step is measuring real conversion before scaling approval. Book a 20-minute conversation to audit your pipeline's approval structure and identify where default-to-automate could collapse weeks of ramp time.

[Schedule a GTM engineering sync →](https://calendly.com/cleiton-sena/gtm-automation-audit)

Or if you're building a similar pipeline: fork the Soilytix repo, run `derive_icp_metrics.py` on your own ICP, and measure. The arch scales to discovery, triage, and approval across any B2B outreach channel.