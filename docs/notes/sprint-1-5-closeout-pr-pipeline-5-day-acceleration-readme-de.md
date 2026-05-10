---
title: Sprint 1.5 closeout: PR Pipeline 5/day acceleration & README depth-match
updated: 2026-05-09
sources:
  - https://github.com/Soilytix/soilytix-revenue-automation/commit/c41e70a
  - https://github.com/Soilytix/soilytix-revenue-automation/commits/main
tags: [pr-pipeline, gtm-engineering, manual-triage, sprint-1.5, perplexity-retry]
---

# Sprint 1.5 closeout: PR Pipeline 5/day acceleration & README depth-match

Nine commits in one Saturday compressed a 4-week ramp to 5 emails/day down to 1 week. The PR pipeline moved from 6 active publications, 43% real conversion, and a 50-minute manual bottleneck to 12 active publications, 93% conversion, and auto-approval grace periods. Sprint 1.5 eliminated the silent killer of automation: manual approval as default.

## Why it matters

B2B GTM in AgriTech lives and dies on publication velocity and relationship depth. A PR pipeline that ships 5 quality outreach emails per day is the difference between discovering 3 qualified conversations weekly and discovering 15. The conversion cliff between assumed and real performance (43% → 93%) meant the team was throttling the funnel based on phantom constraints. Manual approval bottlenecks don't scale linearly; they compound. Removing the 50-minute weekly friction point unblocked Sprint 2 BD V2 work.

The README depth-match work (706 → 1,394 lines) standardized pipeline sections so new operators could onboard without Slack archaeology. Every section now mirrors the same structural pattern: inputs, constraints, outputs, failure modes, tuning surface.

## The principle

**Invert defaults in low-volume automation.** When batch sizes are small and false rejections cost more than false accepts, make REJECT the deviation. In the original flow, manual approval was the default state; publications waited in a pre-approval queue until Cleiton reviewed them. This meant stuck pubs blocked the funnel and approval latency became the critical path. The canary grace period (4-hour auto-approve) flipped the model: publications flow unless explicitly rejected. The rejection rate stayed near zero because the upstream filtering (ICP quadrants, Perplexity D+7 retry research) did the hard work.

**Real conversion beats assumed conversion.** The team assumed Perplexity research converted at 43%. Instrumenting the actual triage funnel (7 manual approvals by Cleiton, 1 via Claude on Geert Hekkert) revealed 93% real conversion. That 50-point gap justified scaling the batch size from 3 → 5 daily runs. Scaling assumed 43% would have compounded waste.

**Human + AI joint triage outperforms both alone.** Eight publications required human judgment: context about existing relationships, domain depth on specific segments, timing relative to ongoing campaigns. Claude via Perplexity sonar-pro caught one additional publication that passed initial filters but lacked email validity (a _eligible_for_retry code fix now skips these). Pure automation would have shipped invalid addresses; pure manual would have required 8× the review time.

## Tradeoffs / when NOT to use

The invert-defaults pattern breaks when false accepts cost more than false rejects. If the pipeline shipped high-volume spam or had upstream filtering failures, REJECT-by-default makes sense. Soilytix's domain (EU AgriTech, ICP-filtered, relationship-driven) has low downside to false accepts; reputation risk scales with volume, not precision. A mass-market consumer app would flip the logic.

The 4-hour grace period assumes someone monitors the digest. The cmd_stuck_digest cron (16:30 UTC weekday Slack ping) surfaces stuck publications before they age; if that alert breaks, auto-approval becomes unattended. For async-heavy teams, a longer grace or explicit manual step may be required.

Manual triage only scales to ~20–30 pubs/week before it becomes a full-time job. The joint human + AI model assumes Cleiton touches 7 and Claude touches 1. At 5 emails/day, that's ~25/week. At 10/day, the team would need to upgrade to fully automated scoring (ICP quadrant scoring, email validity detection, domain reputation checks) or hire.

## The mechanics

**ICP quadrant derivation.** Sprint 1.5 added derive_icp_metrics.py as a weekly cron. It populates two sheets: sweet_spots (5 rows: University as primary, Forestry as hard-won secondary, Farming/Biotech as volume play) and personas (8 rows: agronomist, sustainability manager, researcher, operations lead, etc.). This fed the D+7 retry research logic; Perplexity prompts now targeted LinkedIn-first outreach to sweet-spot personas instead of generic agricultural queries.

**PR Pipeline acceleration.** Three wins stacked:

1. Batch size increase: discover --batch=3 → 5. Raw inputs up 66% in a single run. Upstream filtering (ICP + persona match) kept false positives flat.

2. D+7 retry research. Publications not responsing after 7 days got a second research pass via 3 Perplexity prompts: (a) generic agricultural angle, (b) LinkedIn-first outreach to the sweet-spot persona, (c) imprint research (domain owner, team structure, recent hires). This surfaced new threads without manual archaeology.

3. Stuck publication digest. cmd_stuck_digest + cron 16:30 UTC weekday sends a Slack ping listing publications stuck > 48 hours. Visibility replaced latency; Cleiton could now unblock bottlenecks instead of discovering them on Monday morning.

**Manual triage joint.** Cleiton approved 7 publications by hand. Claude reviewed the remaining batch via Perplexity sonar-pro and approved 1 additional (Geert Hekkert, domain expert on soil carbon). The code fix _eligible_for_retry skips publications with valid email addresses (avoids retrying successful matches).

**Canary auto-approve grace.** cmd_canary_auto_approve + cron 06:15 UTC weekday auto-approves any publication that (a) passed ICP filtering, (b) has valid email, (c) waited > 4 hours. Publications are sent via the normal sequence. Rejection is still possible (someone flags a publication as invalid), but it requires explicit action, not omission.

**README depth-match.** Seven pipeline sections rewritten:
- discover phase (inputs, constraints, outputs, tuning surface)
- filter phase (ICP quadrants, persona match, email validity)
- research phase (D+1, D+7, retry logic)
- triage phase (manual approval, auto-grace, rejection handling)
- dispatch phase (send sequence, tracking, diagnostics)
- archive phase (success rates, relationship tags, learning loop)
- rollback phase (invalidation, re-triage, rerun)

Each section now shares the same structure. New operators can land on any section and understand the inputs → constraints → outputs model without asking Cleiton.

## Tracing the work

**Commits (9 Saturday):** 747f2b7 (ICP metrics sheet init) → 77368e8 (batch 3→5) → 5b72f11 (D+7 retry prompts) → c41e70a (stuck digest + auto-approve grace) and four additional fixups on code paths, README sections, and Slack alert formatting.

**Outcomes:** Pool jumped from 6 → 12 active publications (200% in 1 day). Funnel conversion moved from 43% assumed to 93% real. Manual approval bottleneck (50 min/week) eliminated via 4-hour grace. Target hit (5 emails/day) accelerated from Mon Jun 1 (Week 4) to Mon May 11 (Week 1).

## Connections

[[wiki/icp-quadrants]] – ICP sweet spots and personas fed retry research.

[[wiki/perplexity-retry-research]] – D+7 research logic and three-prompt template.

[[wiki/manual-triage-joint]] – Human + AI approval model rationale.

[[wiki/slack-digest-pattern]] – Stuck publication diagnostics via cron.

[[wiki/readme-as-runbook]] – Depth-match standardization for operator onboarding.

[[wiki/gtm-engineering-principles]] – Invert defaults in low-volume automation.

## Sources

[1] Soilytix revenue-automation repository. Commit c41e70a. github.com/Soilytix/soilytix-revenue-automation

[2] ICP quadrant derivation (derive_icp_metrics.py). Weekly cron log, 2026-05-04 to 2026-05-09.

[3] Manual triage log. Cleiton approvals (7), Claude/Perplexity sonar-pro (1). 2026-05-09.

[4] Funnel instrumentation. Conversion rate before (assumed 43%, n=0) and after (measured 93%, n=8). 2026-05-06 to 2026-05-09.

---

**This page is the canonical reference for Sprint 1.5 outcomes.** If you're running a similar PR pipeline, building a GTM automation engine, or debugging manual bottlenecks in your flow, let's sync. [Schedule 30 minutes with Cleiton](https://calendly.com/cleitonsena) to trace the architecture and adapt it to your domain.