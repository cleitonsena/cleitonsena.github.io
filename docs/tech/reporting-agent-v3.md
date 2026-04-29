---
title: Reporting Agent V3 — Architecture Spec
type: technical
last_updated: '2026-04-28'
status: SPEC v2 — Phase 1 self-funded approved (Decision #8a Apr 28 Julien catchup). Build starts Mon May 4.
authors: Cleiton Sena
---

# Reporting Agent V3 — Architecture Spec

> **One-line:** Replace manual weekly traction reports with an autonomous agent that reads HubSpot + Cockpit + LinkedIn API + Google Ads, narrates the week, and posts to Notion + Slack every Friday 17:00 CET.

> **Why now:** W1/W2/W3 reports were filed Apr 26 (backfilled). Real-time process miss = 0/3. Building V3 closes this for good and removes Cleiton from the bottleneck for every weekly review.

---

## Decision context

This spec exists to convert **Decision #8** in the [Julien Re-onboarding Brief](https://www.notion.so/34df18407014817cb956fb016e096bc6) into concrete build state.

**Apr 28 update — Decision #8a confirmed (Julien catchup):**
- Phase 1 build = **Cleiton self-funded** (personal Anthropic account). No Soilytix budget approval needed.
- Phase 2 expansion (8-agent department) = post 4-week quality gate, decision real Jun.
- Pending Julien Friday: confirm 17:00 CET cadence + Slack DM format.

| Question | Answer |
|---|---|
| Build time | ~5 working days (1 week) — Mon May 4 → Fri May 8 |
| Run cost | **€2-5/mo** (router-on, no separate VM, Langfuse free tier) |
| Maintenance | ~2h/week monitoring + tuning |
| Reversibility | High — kill switch is `pause workflow` in GHA |
| Replaces | Manual report writing (~2h/week Cleiton) + 0/3 real-time miss rate |

---

## Scope (Phase 1 — Reporting Agent ONLY)

This is **slice 1 of the broader Revenue AI Department V3 vision** (8 agents). Scope here = single-agent end-to-end, not the full department. Rationale: validate stack with one agent, instrument cost/quality, then decide Phase 2 expansion.

**In scope:**
- Weekly traction report (Mon-Sun, posts Fri 17:00 CET)
- Daily cockpit health-check (anomaly detection, Slack DM if alert)
- Monthly summary (auto-aggregates 4 weeklies + adds narrative)

**Out of scope (Phase 2+):**
- Reply triage agent
- BD outreach agent (already V0 = `bd_pipeline.py`)
- PR pipeline agent (already V0 = `pr_pipeline.py`)
- Content creator
- Reviewer
- Pipeline health (separate agent)
- Manager/orchestrator (only needed when 3+ agents)

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│  GitHub Actions cron (Fri 17:00 CET / Mon-Fri 08:00 CET)        │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│  Reporting Agent (Claude Agent SDK v0.2.111+)                   │
│  Orchestrator: Opus 4.7 (prompt caching ON)                     │
└────┬───────────┬───────────┬───────────┬───────────┬────────────┘
     │           │           │           │           │
     ▼           ▼           ▼           ▼           ▼
┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌──────────┐
│HubSpot  │ │ Cockpit │ │LinkedIn │ │ Google  │ │ PostHog  │
│ MCP     │ │ Sheets  │ │  Ads    │ │  Ads    │ │  events  │
│         │ │  API    │ │  API    │ │ Reports │ │          │
└─────────┘ └─────────┘ └─────────┘ └─────────┘ └──────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│  Worker: Sonnet 4.6 — narrative generation (caches week schema) │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│  Outputs                                                         │
│  • Notion page in Commercial Board (status Done auto)            │
│  • Slack #soilytix-agent message (TL;DR + link)                  │
│  • soilytix/reports/ markdown file (git-versioned)               │
│  • Cockpit Sheet auto-update (Weekly tab Pipeline + CPL)         │
└─────────────────────────────────────────────────────────────────┘
```

**Single agent, no manager** — at this scope an orchestrator adds latency without value. Manager Agent only kicks in when 3+ workers run concurrently (Phase 2+).

---

## Stack — multi-provider router from Day 1

| Component | Choice | Role | Why | Cost (caching ON) |
|---|---|---|---|---|
| Orchestrator | **Claude Agent SDK v0.2.111+** | Subagent dispatch, MCP, hooks | Mandatory for Opus 4.7 + native Claude alias support | — |
| **Reasoning + planning** | **Opus 4.7** (Anthropic EU) | Decisions in agent loop (only when needed) | High-stakes synthesis. Used sparingly — most weeks Sonnet alone is enough. | €5 / €25 per MTok |
| **Narrative model** | **Sonnet 4.6** (Anthropic EU) | Final write-up of report | 80% cheaper than Opus, indistinguishable for narrative tasks | €3 / €15 per MTok |
| **Long-context reads** | **Gemini 2.5 Flash** (Google EU) — **via LiteLLM** | Pulling HubSpot deals + Cockpit Sheet + Ads APIs into context | **10× cheaper than Sonnet for read-heavy tasks**. 1M ctx window absorbs all reads in one shot. Batch discount 50%. | **€0.30 / €2.50 per MTok** |
| **Anomaly classifier** | **Haiku 4.5** (Anthropic EU) | Daily z-score check on rolling metrics | ~5k tokens/day. Native Claude. | €1 / €5 per MTok |
| **Bulk parsing** (Phase 2) | Llama 3.3 70B (Groq, US) — via LiteLLM | When parsing 100+ deals at once | Cheapest fast tokens. Activates only at scale. | €0.59 / €0.79 per MTok |
| **Multi-provider proxy** | **LiteLLM v1.83.x** (pinned, patched) | Routes Gemini/Llama/Codestral as Claude-aliases for SDK | Claude Agent SDK only speaks Claude aliases natively → LiteLLM is required for non-Claude workers. **Pin exact patched version (NOT v1.82.7/8 — supply chain incident).** | **Python lib (no separate VM)** — `pip install litellm` |
| Runtime | **GitHub Actions cron** | Trigger weekly/daily/monthly | Already used for BD/PR pipelines. Zero new infra. | Free tier sufficient |
| State | **Cockpit Sheet** + Notion DB | Persistence | No new DB needed. | Free |
| Observability | **Langfuse Cloud EU** (free tier) | LLM traces + cost per agent + last-run + kill-button | EU residency (GDPR). 50k observations/mo free — covers Phase 1 + 2 easily. Wire Day 1, not retrofit. | Free tier |

**EU compliance:** Anthropic EU residency (1.1x multiplier accepted), Gemini EU region, Mistral FR-domiciled, Langfuse Cloud EU. No DeepSeek hosted (CSRD/GDPR red flag — see <internal vision doc>).

**Why router from Day 1 (not deferred to Phase 2):** the read step is by far the heaviest in tokens (~30k context per weekly run). Routing reads through Gemini Flash drops Phase 1 cost from €10-20/mo to €2-5/mo, and proves the multi-provider stack works on a low-stakes pipeline before scaling to Phase 2's 8-agent department. **The router IS the Phase 1 architectural validation.**

**Why LiteLLM as Python lib (not VM):** Phase 1 has 1 agent in 1 GHA workflow. A separate VM adds €4.5/mo + DevOps overhead with zero benefit at this scale. `pip install litellm` inside the GHA job is enough. Migrate to Hetzner VM in **Phase 3+** when Cleiton evolves B-level DevOps comfort and 5+ agents share routes (then VM amortizes).

---

## Data sources (read paths)

| Source | What it gives | Auth | Read frequency |
|---|---|---|---|
| HubSpot MCP | Deals (stage, amount, probability), Contacts (last touch), Meetings (booked/held) | OAuth (existing) | Mon morning + Fri pre-report |
| Cockpit Sheet API | Daily ad spend + leads + CPL per channel | Service account (sheets-write-v2) | Daily 08:00 + Fri pre-report |
| LinkedIn Ads API | Campaign-level perf (CPC, CPL, leads) for nuance beyond Cockpit | OAuth (LinkedIn Ads MCP token) | Fri pre-report |
| Google Ads API | Campaign perf + search terms | OAuth (existing reauth flow) | Fri pre-report |
| PostHog API | Funnel events from soilytix.com (post-Klaro consent) | Project API key | Fri pre-report |
| GA4 API | Organic + referral traffic | OAuth (existing) | Fri pre-report |

**Read-only.** No writes to any source except own Notion page + Slack + git repo.

---

## Outputs

### Friday weekly report
- **Notion page** in Commercial Board → Reports section, naming `[Weekly Traction] WX YYYY-MM-DD` (auto-published)
- **Slack DM** to Cleiton + #soilytix-agent channel:
  ```
  📊 W17 Traction Report ready
  TL;DR: LinkedIn CPL €165 (+106% bench, see action plan)
         Pipeline +€[REDACTED] weighted (active deals)
         3 demos booked (-2 vs W16)
  Full report: <notion-url>
  ```
- **Markdown file** in `soilytix/reports/2026-W17-traction-report.md` (git committed by GHA)
- **Cockpit Sheet update** — Weekly tab autopopulated with new row

### Daily cockpit health-check
- Mon-Fri 08:00 CET
- Compares yesterday's metrics vs 7-day rolling avg + benchmarks
- **Silent** unless anomaly detected (CPL >2σ from mean, leads = 0 for 2+ days, spend overrun)
- On anomaly: Slack DM to Cleiton with diagnosis hypothesis (audience fatigue / tracking break / weekend effect)

### Monthly summary
- 1st of month, runs at 09:00
- Aggregates 4 weeklies + adds month-over-month narrative
- Updates Cockpit Monthly tab
- Posts to Notion → Reports

---

## Cost model (realistic, router-on)

### Per weekly run, broken down by step

| Step | Tokens | Model | Provider | Cost |
|---|---|---|---|---|
| Read context (HubSpot + Sheets + Ads + PostHog + GA4) | 30k in | **Gemini 2.5 Flash** (via LiteLLM) | Google EU | **€0.01** |
| Number-tracing validator (every claim → source field) | 2k in, 0.5k out | Haiku 4.5 | Anthropic EU | €0.005 |
| Narrative generation (final report write-up) | 5k cached + 3k out | Sonnet 4.6 (caching ON) | Anthropic EU | €0.05 |
| Orchestration loop (only when complex decisions) | ~1k | Opus 4.7 (rare) | Anthropic EU | €0.005 |
| **Per weekly run total** | | | | **~€0.07** |

### Per daily health-check

| Step | Tokens | Model | Cost |
|---|---|---|---|
| Read yesterday's metrics (Cockpit Daily tab) | 3k in | Gemini 2.5 Flash | €0.001 |
| Anomaly z-score classifier | 5k in, 0.5k out | Haiku 4.5 | €0.007 |
| **Per daily total** | | | **~€0.008** |

### Monthly total (router-on, no VM)

| Cadence | Runs/month | Cost/run | Subtotal |
|---|---|---|---|
| Weekly report | 4 | €0.07 | €0.28 |
| Daily anomaly check (Mon-Fri) | 22 | €0.008 | €0.18 |
| Monthly summary | 1 | €0.10 | €0.10 |
| Anomaly investigations triggered | ~5 | €0.05 | €0.25 |
| **Subtotal LLM API spend** | | | **€0.81** |
| LiteLLM (Python lib in GHA job) | — | — | €0 |
| GHA compute | — | — | €0 (free tier) |
| Langfuse Cloud EU (free tier 50k obs) | — | — | €0 |
| **Total Phase 1 (router-on, no VM)** | | | **€2–5/mo** |

Buffer covers Anthropic API cost spikes + occasional ad-hoc analyses + free tier headroom margin.

**Spec history:**
- v1 Apr 26: €10-20/mo Anthropic-only assumption
- v1.1 Apr 26 evening: €5-7/mo router-on with Hetzner VM
- **v2 Apr 28: €2-5/mo router-on, LiteLLM as Python lib, Langfuse free tier** (current)

**Without caching:** ~€10/mo (still trivial). With caching off + Anthropic-only fallback (worst case): €25-40/mo.

### Phase 2 implications (8-agent department, post 4-week gate)

Earlier brief estimate: **€250-600/mo** (Anthropic-only assumption).
With router (60-70% of read+classify load on Gemini/Groq/Codestral, narrative on Anthropic):
**€80-200/mo** for the full 8-agent dept fully online.

The router IS the cost story. Without it, Phase 2 is hard to greenlight at €600/mo. With it, the same capability is European-residency, multi-provider, and ~3× cheaper.

---

## First 3 actions — Mon May 4 morning (35 min total)

Pre-build setup. Do these BEFORE Day 1 begins. Sequenced for minimum context-switching.

### 1. Setup Langfuse Cloud account EU region (15 min)
- Go to https://cloud.langfuse.com
- **Choose EU region during signup** (GDPR — non-negotiable)
- Create project "soilytix-agents"
- Get API keys: `LANGFUSE_PUBLIC_KEY` + `LANGFUSE_SECRET_KEY` + `LANGFUSE_HOST` (`https://cloud.langfuse.com`)
- Add as GHA secrets in `Soilytix/soilytix-revenue-automation`:
  ```bash
  gh secret set LANGFUSE_PUBLIC_KEY --body "pk-lf-..."
  gh secret set LANGFUSE_SECRET_KEY --body "sk-lf-..."
  gh secret set LANGFUSE_HOST --body "https://cloud.langfuse.com"
  ```
- Verify: dashboard `https://cloud.langfuse.com` opens with empty project — ready to receive traces

### 2. Setup Google AI Studio Gemini API key (10 min)
- Go to https://aistudio.google.com
- Sign in `cleitonsenaa@gmail.com` (existing)
- Create API key (free tier — 60 req/min, plenty for Phase 1)
- Test:
  ```bash
  curl "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=AIza..." \
    -H 'Content-Type: application/json' \
    -d '{"contents":[{"parts":[{"text":"hello"}]}]}'
  ```
- Add as GHA secret:
  ```bash
  gh secret set GOOGLE_AI_API_KEY --body "AIza..."
  ```

### 3. Narrative cadence — committed Apr 29 (10 min mental commitment ✓)

**Personal-only channels (NOT Soilytix corporate accounts).** Build-in-public Karpathy/Tan pattern. First Friday post = **Fri May 8** (after first auto-report ships).

#### Stack pessoal (5 primary + 3 bonus)

| # | Channel | Cadence | Role |
|---|---|---|---|
| 1 | LinkedIn pessoal (post) | Fri 18:00 CET weekly | Hub principal (Soilytix buyers + AI builders EU) |
| 2 | LinkedIn Articles (long-form) | Monthly (1st of month) | Authority + search-indexed |
| 3 | Substack newsletter | Bi-weekly (Sun) | Email list ownership + SEO durable |
| 4 | X.com (@cleitonsena) | Daily build-log threads + Fri cross | AI builders global community |
| 5 | Wiki público GitHub Pages | Continuous push | Karpathy 2nd brain — long-tail SEO |
| 6 (bonus) | Hacker News (Show HN) | One-shot per milestone | Phase 1 done (May 30) + Phase 3 framework OSS |
| 7 (bonus) | Bluesky | Cross-post X automated | Zero extra effort, tech audience migrating |
| 8 (bonus) | Dev.to | Cross-post Substack (canonical → Substack) | EU dev audience |

#### Wk 1-4 May topic backlog

| Wk | Date | LinkedIn topic |
|---|---|---|
| 1 | Fri May 8 | "Built a 5-line agent that replaces my 2h/week manual report. Stack: GHA + Claude SDK + LiteLLM + Gemini Flash + Langfuse. Cost: €X." |
| 2 | Fri May 15 | "First production alert — what the anomaly detector caught that I would have missed." |
| 3 | Fri May 22 | "Cost month-1: €X. Where every euro went (router-on Gemini reads = 80% saving)." |
| 4 | Fri May 29 | "Deciding Phase 2 — which agent next (Reply Triage vs Lead Enrichment) and why." |

#### Daily/weekly choreography

| Day | Channel | Content |
|---|---|---|
| Mon-Thu | X.com | 1-2 build-log tweets/day, learning-of-the-day |
| Fri 18:00 | LinkedIn post + X cross-post | Weekly build-in-public anchor |
| Sun | Substack (bi-weekly Wk 2 + Wk 4) | Long-form synthesis |
| 1st of month | LinkedIn Article + Wiki público update | Case study expansion |

#### Channels skipped + why

| Channel | Reason |
|---|---|
| TikTok | B2B agritech-AI = wrong audience |
| Threads (Meta) | Cross-post X engagement <5% |
| Mastodon | Tech audience migrated to Bluesky |
| YouTube | Time-intensive — defer Phase 3+ when framework OSS ready |
| Reddit | Engagement-heavy + shadow-ban risk for self-promo |
| Medium | Substack > Medium for ownership/SEO |
| Product Hunt | Defer Phase 3+ (framework OSS = launch real) |

#### Soilytix corporate (separate track, NOT this stack)

Reserved for the Soilytix company profile (Bruno/Julien own those channels):
- DLG events / Agritech meetups (in-person)
- Future Farming Magazine (EU trade)
- AgFunder Network newsletter

**Total pre-day setup time:** 35 min real work. Account setup paralelo (Substack + X + Bluesky + Dev.to + Wiki público GitHub Pages) ~2h spread across May Wk 1, NÃO bloqueador do build.

---

## Build plan (5 working days)

### Day 1 — Foundation
- [ ] **Pre-day setup (35 min Mon AM):** Langfuse Cloud EU signup + Google AI Studio Gemini key + GHA secrets wiring. See "First 3 actions" section.
- [ ] Create repo dir `Soilytix/soilytix-revenue-automation/agents/reporting/`
- [ ] `pip install litellm==1.83.x` (pinned patched, post-supply-chain) inside the GHA job — no separate VM
- [ ] Install Claude Agent SDK v0.2.111+ (`pip install claude-agent-sdk`)
- [ ] Wire all 6 MCPs (HubSpot, Sheets, LinkedIn Ads, Google Ads, PostHog, GA4) — already exist, just connect
- [ ] **Langfuse traces wired Day 1, not retrofit** — every LLM call instrumented from the first token

### Day 2 — Read paths
- [ ] HubSpot deal-stage snapshot function (input: week range; output: structured dict)
- [ ] Cockpit Sheet read function (input: Weekly tab range; output: dict)
- [ ] LinkedIn Ads + Google Ads campaign perf functions
- [ ] PostHog + GA4 funnel snapshot
- [ ] **Smoke test:** dump all 6 sources to JSON, verify shapes

### Day 3 — Narrative generation
- [ ] Prompt design: weekly report template (TL;DR / By the numbers / Wins / Risks / Next week focus)
- [ ] Sonnet 4.6 narrative call with cached system prompt + cached schema
- [ ] **Quality gate:** generate 3 reports against W14/W15/W16 data, compare vs human-filed Cleiton reports — should pass blind test

### Day 4 — Outputs
- [ ] Notion page creation in Commercial Board (using existing MCP or notion-cli once MKT-OPS-03 lands)
- [ ] Slack message via webhook (reuse #soilytix-agent webhook from PR pipeline)
- [ ] Markdown file commit (auto-PR or direct push to main with skip-ci)
- [ ] Cockpit Sheet auto-write (sheets-write-v2 creds)

### Day 5 — Cron + observability
- [ ] GHA workflow `.github/workflows/reporting-weekly.yml` (cron `0 16 * * 5`)
- [ ] GHA workflow `.github/workflows/reporting-daily.yml` (cron `0 7 * * 1-5`)
- [ ] Anomaly detection function (z-score on rolling 7-day window)
- [ ] **Langfuse dashboard:** cost per report, latency per LLM call, error rate, kill-button per agent
- [ ] Run end-to-end Fri May 8 17:00 CET — **first auto-report ships**

### Buffer / Polish (week 2 if needed)
- [ ] Tune narrative prompts based on Julien feedback
- [ ] Add Linear-style "Next week focus" section auto-derived from open Notion tickets
- [ ] Add weekly emoji header (🚀 / 🟡 / 🔴) based on overall traction score

---

## Quality gates (before Julien greenlight Phase 2)

Run for 4 consecutive weeks (May Wk 2 → Wk 5 = May 8 → May 31). Phase 2 only if:
- [ ] **Cost actual** ≤ €10/mo (vs €2-5 estimate, with buffer)
- [ ] **Quality** — Julien rates 4 of 4 reports ≥ 7/10 vs Cleiton baseline
- [ ] **Reliability** — 0 missed weekly runs (auto-recovery on transient failures)
- [ ] **Time saved** — Cleiton spent <30 min/week reviewing/editing (vs 2h writing)
- [ ] **No data leak** — Langfuse traces show 0 unauthorized writes outside scoped sources

If 1+ gate fails: iterate Phase 1, do not expand to Phase 2.

---

## Risks + mitigations

| Risk | Severity | Mitigation |
|---|---|---|
| Opus 4.7 new tokenizer surprise costs | Medium | Cap weekly spend at €5 via Anthropic API budget alert. Fall back to Sonnet 4.6 orchestrator if exceeded. |
| LiteLLM future supply chain incident | Medium | Pin exact version (v1.83.x specific patch). Renovate bot for security-only updates. |
| HubSpot/LinkedIn API rate limits | Low | Cache reads with 1h TTL; nightly batch instead of real-time. |
| Narrative drifts (Julien hates the voice) | Medium | Quality gate week 1 — if drift, re-prompt with Cleiton's W1-W3 reports as few-shot examples. |
| Hallucinated numbers | High | **Hard gate:** every number in narrative must trace to a source field. Validator function asserts before publish. Fail closed (skip publish, alert). |
| Anomaly false positives (Slack noise) | Low | Tune z-score threshold week 1-2. Allow user `mute` command in Slack. |

---

## Phase 2 trigger criteria

Build Reply Triage Agent (next slice) only when:
1. Phase 1 ran 4 consecutive weeks without manual intervention
2. Real cost ≤ €10/mo confirmed
3. Julien explicitly greenlights with "yes, go to Phase 2"
4. Langfuse dashboard shows clean trace flow (no investigation backlog)

Phase 2 expected scope: BD reply triage (Haiku 4.5 native classify into 5 buckets) + Gemini 2.5 Flash for prospect research enrichment.

---

## References

- [Anthropic — Building Effective Agents PDF](https://resources.anthropic.com/hubfs/Building%20Effective%20AI%20Agents-%20Architecture%20Patterns%20and%20Implementation%20Frameworks.pdf)
- [Claude Agent SDK overview](https://code.claude.com/docs/en/agent-sdk/overview)
- [LiteLLM providers](https://docs.litellm.ai/docs/providers)
- [memory] <internal vision doc> (V3 vision Apr 26)
- [memory] <internal architecture note — see public PR pipeline source code>
- [ticket] MKT-OPS-04 (build tracker — to be created)
- [brief] [Julien Re-onboarding Brief](https://www.notion.so/34df18407014817cb956fb016e096bc6) Decision #8

---

*Filed Apr 26 2026 by Cleiton Sena. Status: SPEC — awaiting Julien approval Tue 28 Apr in 1:1.*
