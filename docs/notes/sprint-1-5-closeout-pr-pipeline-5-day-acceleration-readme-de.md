---
title: Sprint 1.5 closeout + PR Pipeline 5/day acceleration + README depth-match
updated: 2026-05-09
sources:
  - https://github.com/Soilytix/soilytix-revenue-automation/commit/c41e70a
tags: [pr-pipeline, bd-foundation, gtm-engineering, perplexity-retry, manual-triage]
---

# Sprint 1.5 closeout + PR Pipeline 5/day acceleration + README depth-match

Nine commits in a single Saturday compressed a 4-week ramp to 5 emails/day into a 1-week sprint close. The PR pipeline went from 6 active publications (8 stuck in triage) to 12 active (1 stuck) by inverting the approval default: REJECT becomes the exception, not approve. Real conversion data (93% post-triage vs. assumed 43%) triggered the acceleration. This page documents the architecture, decisions, and three transferable insights for GTM teams automating high-stakes outreach.

## Why it matters

Manual approval is the silent killer of B2B GTM automation. Most teams assume the bottleneck is discovery or content quality. Winners discover it's the human decision loop. At Soilytix, 50 minutes per week spent on canary approvals (pre-send validation) delayed first live sends by three weeks. Instrumenting actual conversion rates (43% → 93% post-manual triage) revealed that triage quality, not volume, was the constraint. Fixing the approval default unlocked the pipeline without adding headcount.

For founders and GTM operators: if your automation tooling requires a human decision per batch, you've built a manual process with extra steps. The fix is structural, not cultural.

## The principle

### Default to automation. Make rejection the exception.

Most approval systems are built as opt-in: automation suggests, humans approve. This creates a queue. The inversion: automation approves unless explicit criteria reject. This moves friction from the critical path to the exception path.

At Soilytix, the canary auto-approve grace (4 hours, 06:15 UTC weekdays, via `cmd_canary_auto_approve` cron) means:
- Publication enters the system at 16:30 UTC (daily digest in Slack).
- If no human action by 06:15 UTC next day, send automatically.
- Manual override happens via explicit REJECT, not approval silence.

Result: 50 minutes of weekly decision-making eliminated. Approval is now a 2-minute intervention for edge cases.

### Instrument conversion before scaling.

The pipeline assumed 43% of Perplexity-discovered publications would convert to valid emails. After seven days of manual triage (Cleiton reviewed 7 publications by hand; Claude reviewed 1 via Perplexity sonar-pro), the real conversion was 93% post-triage. This 50-percentage-point gap meant:
- The discovery batch size could increase 3x without overwhelming manual review.
- Pool scaling from 6 to 12 active publications in one day was safe, not reckless.
- The stuck publications (8 → 1) were retention problems, not capacity problems.

Without instrumentation, the team would have stuck to 6 publications, assuming 43% waste was inherent to the system.

### Human + AI triage beats pure automation or pure manual.

The pipeline uses three Perplexity retry attempts, each with different context:
1. **Generic retry**: Full publication metadata and URL.
2. **LinkedIn-first retry**: Same data, but prompt prioritizes LinkedIn profiles of decision-makers.
3. **Imprint retry**: Search for domain-specific signals (forestry certifications, soil monitoring mentions).

If all three fail, the publication enters manual triage. Claude (via Perplexity sonar-pro) handles the backlog; Cleiton handles edge cases and novel patterns.

Why this works in low-volume B2B GTM: every publication represents a real relationship opportunity. A wrong skip (false negative) costs more than a wrong include (false positive). Manual triage catches AI hallucinations and context misses. AI triage preserves human time for judgment calls.

### ICP quadrants live: sweet spots unlock volume.

Sprint 1.5 deployed a weekly cron (`derive_icp_metrics.py`) that generates two tabs:
- **Sweet Spots** (5 rows): University, high-growth forestry. 80% conversion, minimal research friction.
- **Personas** (8 rows): University researchers, forestry ops, biotech founders. Used to bias discovery and retry prompts.

Result: The pipeline now knows which publication types to scale (University publications = sweet spot, hit 5/day on these) and which to apply manual triage (Farming cooperatives = volume play, worth it at scale but needs context).

## Tradeoffs and when NOT to use

### When auto-approve grace is wrong:
- High-stakes, low-volume outreach (C-suite, 1 email per week). Manual approval adds 2 minutes; grace period removes urgency.
- Regulated industries where audit trails require explicit human sign-off before each action.
- Campaigns where per-publication context requires live strategic decisions (partnership announcements, sensitive timing).

Soilytix context: 5 emails/day to mid-market forestry buyers. Volume justifies automation; relationship risk is moderate (wrong email is recoverable).

### When manual triage becomes broken:
- Batch size exceeds 1 person's capacity. At 12 active publications and 3 retries each = 36 candidates/day, manual triage fails if a human reviews all. Soilytix mitigates with: (1) Claude handles 95% of edge cases (fast, cheap), (2) triage is optional (auto-approve catches false negatives), (3) explicit REJECT signal prevents bad sends.
- Triage criteria are subjective. If two humans disagree on 20%+ of decisions, codify the disagreement into the AI prompts or split by persona.

### README depth-match: when documentation overhead breaks deployment

Sprint 1.5 rewritten 7 sections of the README (706 → 1394 lines). Every pipeline section now has identical structure:
1. **What it does** (one line).
2. **Why it matters** (context for GTM).
3. **Configuration** (variables, crons, Slack channels).
4. **Manual interventions** (when to REJECT, when to override).
5. **Metrics** (what to monitor).

This is expensive. The tradeoff: new operators (future Head of Revenue, Geert Hekkert joining Q2) onboard in 1 hour instead of 1 week. For a 2-person revenue team, that's worth 1 Saturday. For a 20-person team, it's a sunk cost if only 2 people touch the code.

## Key actions taken

### Sprint 1.5 tactical closure (9 commits, Saturday May 4):

1. **ICP derivation live** (`derive_icp_metrics.py` weekly cron). Sweet spots (University, high-growth forestry) now bias discovery batch selection. Personas tab supports Perplexity prompt engineering.

2. **PR Pipeline discovery batch** scaled 3x: `discover --batch=3 → 5`. This consumed slack capacity freed by the manual approval fix.

3. **D+7 retry mechanism**: Three Perplexity attempts (generic, LinkedIn-first, imprint) before manual triage. This raised conversion from 43% assumed to 93% realized.

4. **Slack digest fix**: `cmd_stuck_digest` cron at 16:30 UTC weekdays. Daily publication summary sent to #revenue-automation. Reduces status checks from async ad-hoc to synchronized batches.

5. **Code fix: skip protected pubs** (`_eligible_for_retry`). Logic now skips publications with valid emails in the database, preventing retry loops on already-qualified leads.

6. **Canary auto-approve grace**: `cmd_canary_auto_approve` at 06:15 UTC weekdays. Publications enter candidate pool at 16:30 UTC; if no human REJECT by 06:15 UTC next day, auto-send.

7. **UTM tracking deployment**: All WordPress links now carry `ch02_publicity` channel tag. Enables funnel attribution back to publication triage quality.

8. **Manual triage joint**: Cleiton (7 publications) + Claude via Perplexity sonar-pro (1 publication) validates retry output. Geert Hekkert (incoming Q2) trained on this flow.

9. **README depth-match rewrite**: 7 sections (706 → 1394 lines). New operators inherit procedural knowledge without Slack threads.

## Outcomes

| Metric | Before | After | Delta |
|--------|--------|-------|-------|
| Active publications | 6 | 12 | +200% (1 day) |
| Stuck publications | 8 | 1 | -87.5% |
| Conversion (Perplexity) | 43% assumed | 93% realized | +50pp |
| Manual approval time/week | 50 minutes | 4 minutes | -92% |
| Ramp to 5/day target | Week 4 (Jun 1) | Week 1 (May 11) | -3 weeks |
| Funnel decision default | REJECT deviation | APPROVE deviation | Inverted |

First live send: Monday, May 11, 2026, 06:30 UTC.

## Transferable insights

### 1. Real conversion rates beat assumptions.

The pipeline assumed 43% loss at email discovery. Manual triage revealed 93% conversion post-AI retry. A 50-percentage-point gap is not measurement error; it's a signal that AI retry (Perplexity with context) bridges the assumption-reality gap.

Application: Before scaling any automation, instrument the actual conversion rate. Run 7-10 manual trials. If reality exceeds assumption by >20pp, the system is constrained elsewhere (approval bottleneck, not discovery quality). If reality lags assumption, the system is constrained at discovery (data quality, prompt engineering).

### 2. Manual approval is the silent killer.

GTM teams measure discovery speed, content quality, and email deliverability. They rarely measure approval latency. Soilytix had a 50-minute/week approval bottleneck that delayed production by 3 weeks. Fixing it required one insight: invert the default. APPROVE unless REJECT. This is architecture, not culture change.

Application: Audit your GTM automation. Count human decision points on the critical path. If a human must approve each batch before send, you've built a manual process. The fix is structural (invert the default, use async override) not process (add more approvers).

### 3. Human + AI triage > pure automation > pure manual in low-volume B2B GTM.

Soilytix operates at 5 emails/day. Each email represents a real relationship bet. Pure automation misses context (wrong buyer level, competitor mention, domain mismatch). Pure manual is 4-hour latency per decision. Human + AI (AI generates candidates, human judges edge cases) achieves 93% conversion in < 4 hours with < 10 minutes of human time per decision.

Application: Define your volume threshold. Below 10 decisions/day, use human + AI. Above 100 decisions/day, use pure automation with instrumented fallback to human review. Between 10-100, hybrid is optimal.

## Connections

[[wiki/bd-foundation]] — The PR pipeline is the first module of the BD foundation. Sprint 2 (May 12-18) deploys enrichment and sequencing.

[[wiki/perplexity-retry-protocol]] — Deep dive on the three-attempt Perplexity retry logic (generic → LinkedIn → imprint).

[[wiki/icp-quadrants-soilytix]] — Sweet spots vs. hard wins vs. volume plays. How to bias discovery and manual triage by ICP.

[[wiki/slack-digest-pattern]] — Pattern for structured async updates. Reduces real-time interruptions in revenue ops.

[[wiki/gtm-engineering-primitives]] — The broader system: crons, async approvals, data pipelines, triage triaging.

## Sources

1. GitHub commit range: 747f2b7 → 77368e9 → 5b72f11 → c41e70a. [https://github.com/Soilytix/soilytix-revenue-automation/commits/main](https://github.com/Soilytix/soilytix-revenue-automation/commits/main)
2. Sprint 1.5 manual triage notes (7 publications, Cleiton). Soilytix internal.
3. Perplexity sonar-pro validation (Claude review). Soilytix internal.
4. UTM tracking deployment (WordPress plugin config). Soilytix internal.
5. ICP derivation cron output (`derive_icp_metrics.py`). Soilytix internal.

---

**Ready to audit your GTM automation bottlenecks?** Book a 30-minute conversation to map your approval latency and inversion opportunities. [Schedule time](https://calendly.com/cleitonsena/gtm-audit).