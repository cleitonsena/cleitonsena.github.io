---
title: Agent Management Architecture — Soilytix + Personal
type: concept
category: AI Engineering & Org Design
last_updated: '2026-04-28'
status: planning reference for May 2026 Phase 1 build + Jun 2026 Phase 2 trigger
tags:
- ai-agents
- control-plane
- observability
- governance
- engineering-process
- saas-stack
sources:
- ../people/garry-tan.md
- ../concepts/thin-harness-fat-skills.md
- ../concepts/ai-native-company.md
- ../concepts/20x-companies.md
- ../tools/g-stack.md
- ../tech/reporting-agent-v3.md
related_companies:
- Paperclip (paperclip.ing)
- Multica (multica.ai)
- Langfuse (langfuse.com)
- Linear (linear.app)
- Cursor (cursor.com)
---

# Agent Management Architecture — Soilytix + Personal

> Reference document for AI agent department gestão em Soilytix + Cleiton's personal projects. Filed Apr 28 2026 antes do Reporting Agent V3 build (May Wk 1-2). Re-read antes de Phase 2 trigger (May Wk 3+) e antes Phase 3 review (Set-Out 2026).

## TL;DR

Agent management **não é uma coisa só** — são **8 layers** com tools diferentes. Para Soilytix (multi-stakeholder, EU GDPR, low-budget) o stack ideal Apr 2026 é **managed SaaS-first** (não self-host até Cleiton evoluir DevOps comfort). Para personal track (xquads-evolved), os mesmos primitives reusam mas com governance light. **3 sistemas docs/tasks ficam como are**: Notion (strategic) + GitHub (technical) + Obsidian wiki (knowledge synthesis). Linear, Cursor, Paperclip, Multica off the table até signals concretos. Plano: ship Reporting Agent V3 May Wk 1-2 com observability wired desde Day 1, Phase 2 trigger May Wk 3+, 8-agent department target Out 2026.

---

## 🧬 Os 8 layers de agent management

Antes de escolher tools, decompor o problema:

| # | Layer | O que decide | Soilytix critical | Personal critical |
|---|---|---|---|---|
| **0** | **Identity** | Quem é cada agent? Que credenciais? Audit trail? | ⭐⭐⭐ (multi-stakeholder + GDPR) | ⭐ (só Cleiton) |
| **1** | **Runtime** | Onde corre? Cron? Interactive? VM? | ⭐⭐ (cron + reliability) | ⭐⭐ (interactive + experimental) |
| **2** | **Memory** | Lembra entre runs? Cross-session? Cross-agent? | ⭐⭐ (week-over-week reports) | ⭐⭐⭐ (continuidade pessoal) |
| **3** | **Communication** | Como fala com humanos? Outros agents? APIs? | ⭐⭐⭐ (Notion/Slack/HubSpot) | ⭐ (Slack/files) |
| **4** | **Observability** | O que fez, porquê, quanto custou, falhou? | ⭐⭐⭐ (cost discipline + audit Bruno) | ⭐⭐ (curiosidade + cost) |
| **5** | **Coordination** | Task queue. Assignment. Dependências. | ⭐⭐ (Phase 2+) | ⭐ (single-task usually) |
| **6** | **Governance** | Budgets. Approval gates. Kill switches. | ⭐⭐⭐ (Bruno visibility + €) | ⭐ (só carteira tua) |
| **7** | **Skills/Capability** | Reusable patterns. Prompt library. Tools. | ⭐⭐ (3 candidate skills filed) | ⭐⭐⭐ (xquads-squads existe) |

**Insight crítico:** os tools NÃO competem entre si — endereçam **layers diferentes**:

- **Langfuse** = Layer 4 (observability)
- **Paperclip** = Layer 6 (governance) + parcial 5 (coordination)
- **Multica** = Layer 5 (coordination) + parcial 7 (skills)
- **gstack** (Garry Tan) = Layer 7 (skills) + parcial 1 (runtime patterns)
- **Notion** = Layer 3 (communication) + parcial 5 (coordination)
- **gbrain** = Layer 2 (memory)
- **LiteLLM** = Layer 1 (runtime — model routing)
- **Linear** = Layer 5 (coordination) + parcial 3 (comms)
- **Cursor** = development tool, **NÃO endereça nenhum layer agent management**

Vais usar **vários, não um.**

---

## 🌱 Soilytix track — multi-stakeholder, GDPR, revenue-critical

### Constraints únicos

- **3 stakeholders:** Cleiton (Head of Revenue), Bruno (CEO, results-driven, não dashboard-deep), Julien (CCO)
- **GDPR + CSRD (EU)** — data residency hard requirement
- **Revenue-critical** — failures = perda de €
- **Budget low-radar** — < €30/mo Phase 1+2 sob radar Bruno
- **Cleiton DevOps comfort:** SaaS-only por agora, evolve para self-host Q3+

### Stack final (managed-only, Apr 2026 decision)

| Layer | Tool | Cost/mês |
|---|---|---|
| 0 Identity | Shared OAuth (Cleiton) + per-agent Slack apps Phase 2+ | €0 |
| 1 Runtime | GHA cron (free) + LiteLLM as Python lib (no separate VM) | €0 |
| 2 Memory | Notion DB row "agents" + Cockpit Sheet | €0 |
| 3 Communication | Notion (Commercial Board) + Slack #soilytix-agent | €0 (existing) |
| 4 Observability | **Langfuse Cloud EU free tier** (50k obs/mo) | €0 |
| 5 Coordination Phase 1 | Notion DB | €0 |
| 5 Coordination Phase 2+ | **Notion DB extended OR Linear free** (250 issues) | €0 → €8/mo Phase 3 |
| 6 Governance | Anthropic budget alert + monthly review email Bruno | €0 |
| 7 Skills | GitHub `agents/skills/` markdown + xquads converted | €0 |
| **API spend Phase 1** | Anthropic + Gemini Flash | **€2-15/mo** |
| **API spend Phase 2** | + 2-3 agents | **€30/mo** |
| **API spend Phase 3** | 5+ agents | **€60-80/mo** |
| **API spend Phase 4** | 8 agents department | **€100-150/mo** (precisa Bruno report) |
| **Hetzner VM (post Q3)** | Quando Cleiton evolve DevOps comfort | €4.5/mo |

### Output design — Bruno-friendly (Q1: results-driven)

Não é dashboard. É **Slack DM weekly Friday + immediate alerts**:

```
📊 W19 Traction Friday Report
ROAS: 4.2× (+0.5 vs W18) ✅
Pipeline: +€7,500 weighted (3 new deals)
Demos booked: 18 (+4)
Leads: 42 (-2, monitoring)
Top alert: nenhum esta semana
Full report: <Notion link>
```

Quando algo anómalo (W3 spike-style):
```
🚨 LinkedIn CPL spike — €165 (+106% vs benchmark)
Action plan filed: <link>
Hipóteses: fatigue OR attribution lag
```

### Per-phase timeline (compressed Apr 2026 decision)

#### Phase 1 — May Wk 1-2 — Ship V3 + start narrative

**Build:**
- Reporting Agent V3 — single agent, Stack C managed
- Anthropic Sonnet 4.6 (narrative) + Gemini 2.5 Flash (reads) + Haiku 4.5 (anomaly)
- LiteLLM as Python lib (não VM separate)
- GHA cron orchestration + Langfuse Cloud EU traces wired Day 1
- Notion + Slack outputs

**Cost real Phase 1:** ~€2-5/mo

**Narrative (build-in-public):**
- LinkedIn post sobre o build
- Soilytix blog post (uses Blog Pipeline GHA shipped Apr 27)
- Adicionar a `soilytix/content/blog-drafts/`

#### Phase 2 — May Wk 3 → Jun — Compress Jun-Aug original plan

**Build:**
- Agent #2: Reply Triage (Haiku 4.5 classify replies)
- Agent #3: Lead Enrichment ou Content Reviewer
- **Custom Streamlit dashboard** 1 day — agents alive + cost per agent + last-run + kill-button (replaces Multica/Paperclip self-host need)

**Decision points:**
- Notion API rate limit começou a partir? → migrar tickets para Linear free
- Senão → mantém Notion DB

**Narrative:** LinkedIn weekly + 1 podcast / livestream pitch

#### Phase 3 — Jul-Aug — Scale to 5 agents + framework emerges

**Build:**
- Agents 4-5
- Personal track parallel: xquads → SKILL.md migration
- LiteLLM proxy passa Hetzner VM (€4.5/mo) quando Cleiton evolve B-level

**Narrative + Framework:**
- "Soilytix Stack Open Sourced" — abstract patterns em framework reusable, MIT license, separate repo
- Speaking gigs (DLG, Agritech meetups, AI conferences EU)

#### Phase 4 — Set-Out — 8 agents + cement authority

**Build:**
- 6-8 agents production
- Personal projects on same patterns (proves replicability)
- Framework GitHub repo: 1k+ stars target

**Narrative + Career:**
- 1-2 keynote talks / podcasts
- Wiki público (subset cleitonOS-workspace)
- Soilytix promo discussion (cabe a Bruno)

---

## 🧪 Personal track — solo, exploratório, memory-rich

### Constraints únicos vs Soilytix

- 1 stakeholder (Cleiton)
- Sem GDPR pressure
- Failures = oportunidade aprender
- Budget pessoal flexível
- **Existing investment:** xquads-squads (13 squads markdown), ti-na-europa, autoresearch-mlx, Obsidian, MCP custom servers

### Asset escondido — xquads-squads

Tens 13 squads markdown em `xquads-squads/`. Hoje são static documents. **A migração óbvia:** transformar em **executable SKILL.md format** estilo gstack:

```yaml
---
name: hormozi-offer-stress-test
description: Apply Hormozi value equation framework to stress-test an offer
allowed-tools: [Read, Write, AskUserQuestion]
triggers:
  - "stress test this offer"
  - "is this offer good"
---

## Forcing questions
1. Dream Outcome: que resultado específico cliente atinge?
2. Perceived Likelihood: que evidência tens que isto vai funcionar?
3. Time Delay: quanto tempo entre purchase e value?
4. Effort & Sacrifice: o quanto cliente tem de fazer?

## Anti-patterns
- Vague outcomes ("save time")
- Hypothetical testimonials
- Claims without proof

## Output
Score 0-10 + 3 specific revision suggestions.
```

Resultado: tens **personal gstack equivalent** com tua wisdom curada.

### Stack por layer — Personal

| Layer | Tool | Por quê |
|---|---|---|
| 0 Identity | Cleiton OAuth + 1 GitHub PAT + 1 Slack PAT | Single-user, não vale per-agent |
| 1 Runtime | Claude Code interactive (default) + GHA cron | Interactive para experimentação, GHA recorring |
| 2 Memory | gbrain (Garry Tan) cross-machine + Obsidian wiki | Cleiton M4 + Mac empresa partilham contexto |
| 3 Communication | Telegram bot @StealthOps + Obsidian | Mobile + deep work |
| 4 Observability | Langfuse Cloud EU free (compartilhada com Soilytix se quiseres) | Free, EU, dual-purpose |
| 5 Coordination | xquads-squads/ + GitHub issues per project | Não vale Multica para 1 user |
| 6 Governance | Anthropic console budget alert mensal | Disciplina mental, não tool |
| 7 Skills | xquads → SKILL.md format + clone gstack patterns | 13 squads → executable |

### Action concreta Personal (próximas 4 semanas, post-Soilytix Phase 1)

Não competes com May Wk 1-2 (Soilytix prioridade). Personal track arranca **May Wk 3+**:

#### Week 1 personal (~30-60 min weekend)
- Clone `garrytan/gstack` localmente
- Estudar 3 SKILL.md (office-hours, plan-ceo-review, design-shotgun)
- Pick 1 xquad — recomendo **hormozi-squad** (aplicável a Soilytix copy também)
- Converter em `xquads/skills/hormozi-offer-stress-test/SKILL.md`
- Test: invocar via Claude Code, verificar framework é seguido

#### Week 2-3 personal
- Converter 4-5 squads top-priority
- Recommended order:
  1. hormozi-squad (offer/copy review)
  2. copy-squad (LinkedIn posts, outreach)
  3. claude-code-mastery (meta — agents melhores)
  4. advisory-board (decision-making)
  5. storytelling (content)

#### Week 4 personal
- Setup gbrain self-host (Hetzner VM mesma instance se Cleiton evolved B-level, ou separate)
- Cross-machine memory: M4 home + Mac empresa share contexto

#### Mês 2-3 (Jun-Jul)
- Ti-na-europa: aplicar mesmo pattern — `agents/` folder skill library
- Telegram bot: wire skills via Claude Code subprocess (já tens shadow-mode)

---

## 🤝 Primitives partilhados — build once, use both

| Componente | Soilytix usa para | Personal usa para |
|---|---|---|
| **Hetzner CX22 VM €4.5/mês** (post Q3) | LiteLLM dedicated + Langfuse self-host | gbrain server + same VM |
| **Langfuse Cloud EU** (Phase 1-2) | Reporting Agent traces + cost | xquads runs traces |
| **gbrain memory** (Q3+) | Cross-machine Cleiton state | Memory cross-session entre projetos |
| **GitHub Actions free tier** | BD/PR/Blog/Cockpit pipelines | Personal cron (research, content gen) |
| **Anthropic API key Cleiton pessoal** | Phase 1 V3 (€5-7/mo) | Personal experiments |
| **Skill library pattern markdown SKILL.md** | Soilytix `agents/skills/icp-stress-test/SKILL.md` | xquads transformados |

**Big insight:** mesma VM Hetzner serve ambos quando Cleiton evolui B-level (Q3+). €4.5/mês cobre LiteLLM + Langfuse self-host + gbrain. Personal e Soilytix compartilham **infrastructure**, mantêm **identity separate** (databases isolados, secrets diferentes).

---

## 📚 Documentation stack — 3 systems naturais

**Linear off the table.** Cursor não cabe. **Stack actual stays:**

```
Strategic / Business / Tickets
    └─→ Notion (Commercial Board)
         tasks, decisions, ICP analysis, deal pipelines, OKRs

Technical / Code / Stack
    └─→ GitHub (repo READMEs, wikis)
         soilytix-revenue-automation/README.md
         per-repo docs, commit-history-driven

Knowledge / Synthesis / People / Concepts
    └─→ Obsidian wiki (cleitonOS-workspace/wiki/)
         frameworks, gurus, concepts, master-map
         Karpathy-style 2nd brain
```

**Cada um tem natural fit:**
- Bruno (CEO) lê Notion — vê pipeline, decisões, briefs
- Engineers lêem GitHub READMEs — vê deploy, contribuir, debug
- Cleiton sintetiza em Obsidian wiki — synthesise across all sources

**Triggers para revisitar Linear (Out 2026+):**
- Notion API começar a partir agents (rate limit hits weekly)
- Equipa cresce 5+ people active em tickets daily
- Velocity-driven workflows (sprints rigorous) em vez de strategic-driven

---

## ❌ Tools considered + rejected (Apr 2026)

### Cursor (cursor.com)
- **Categoria:** AI-powered IDE + cloud agents para coding
- **Por que rejeitado:** wrong layer. Não endereça layers 0-7 agent management. Switching cost real (Cleiton já tem Claude Code CLI muscle memory). No EU residency stated → GDPR risk se touches customer data.
- **Quando revisitar:** Bruno (CEO) ou future eng hire para product code, não Cleiton GTM ops.

### Paperclip (paperclip.ing)
- **Categoria:** Layer 6 governance + parcial 5 coordination, "AI agents as employees" metaphor
- **Por que rejeitado AGORA:** self-host first → Cleiton C-level DevOps comfort, não vai correr VM Phase 1+2.
- **Quando revisitar:** Q3+ quando Cleiton evolve B-level OR Paperclip lançar Cloud SaaS

### Multica (multica.ai)
- **Categoria:** Layer 5 coordination + parcial 7 skills
- **Por que rejeitado AGORA:** mesma razão que Paperclip — self-host required.
- **Quando revisitar:** Q3+ ou Cloud lançar

### Linear (linear.app)
- **Categoria:** Layer 5 coordination (tickets) + parcial 3 comms
- **Por que rejeitado AGORA:** migration cost Notion → Linear = sprint inteiro. Bruno + Julien já lêem Notion. Notion API rate limit ainda não nos parteu.
- **Quando revisitar:** Out 2026+ se Notion API rate limit hits weekly OR equipa cresce 5+ users

### Bedrock AgentCore (AWS)
- **Por que rejeitado:** lock-in AWS desnecessário. Não estamos em AWS.

### Microsoft Copilot Studio
- **Por que rejeitado:** $200/mo min tenant. Caro. Lock-in Microsoft.

### OpenAI Agent Builder / Swarm
- **Por que rejeitado:** lock-in OpenAI. Stack já é Anthropic + Google + Meta (LiteLLM router).

---

## 🎯 First 3 actions to start IMMEDIATELY (May Wk 1)

### 1. Setup Langfuse Cloud account EU region (15 min)
- https://cloud.langfuse.com — escolher EU region durante signup
- Get API keys + project ID
- Add `LANGFUSE_PUBLIC_KEY` + `LANGFUSE_SECRET_KEY` GHA secrets
- **Wire em Day 1 do Reporting Agent V3 build** (não retrofit depois)

### 2. Setup Google AI Studio free tier Gemini API (10 min)
- https://aistudio.google.com — free tier generoso
- Get API key
- Add `GOOGLE_AI_API_KEY` GHA secret
- LiteLLM Python lib config: route reads através disto

### 3. Decide narrative cadence (10 min mental + commitment)
- LinkedIn post: weekly Friday build-in-public, 1 specific learning per week
- Soilytix blog: monthly comprehensive case study (uses Blog Pipeline GHA)
- Wiki public excerpt: quando 3-month milestone (Jul)

**Total setup time:** 35 min real work. Tudo manual, sem Hetzner, sem Docker.

---

## ⚠️ Reality check honesto — Q5 success em 6 meses

Cleiton's stretch goal Out 2026: **8 agents + tech authority + C-level promotion + internet authority + rich**.

Probability per outcome:

| Outcome | Probability achievable em 6 meses | Comment |
|---|---|---|
| 8 agents running | **65%** | Stretch mas possível com aggressive timeline + framework reuse. Mais provável 5-6 agents reais + 2-3 stub. |
| Tech authority on AI-native | **40%** | Depende de **content discipline** (1 LinkedIn post/week minimum). Sem isso, zero. |
| C-level promotion Soilytix | **30%** | Depende Bruno calibration + Soilytix revenue trajectory + funding round. Não controlas tudo. |
| Internet authority | **35%** | 6 meses é curto. Precisa 1-2 viral pieces + sustained content. |
| Rich from personal | **15%** | 6 meses é cedo demais para revenue real de personal projects. 18-24 meses mais realista. |

**Honest framing:** ambition is healthy AND **all simultaneously em 6 meses é unrealistic**. **Pick 2-3 prioritários:**

1. **8 agents Soilytix** (control)
2. **Tech authority** (controlable via content discipline)
3. **C-level / wealth** = downstream effects, deixa happen naturally

---

## 🔓 Migration safety — não lock-in

Princípios para escolhas hoje não viram prisão amanhã:

1. **Skills em markdown SKILL.md format** — portável entre Claude Code, gstack, Multica, custom runtime
2. **Memory em Postgres standard** — gbrain SQLite/Postgres simples, exportável
3. **Traces em OpenTelemetry format** — Langfuse exporta, qualquer observability importa
4. **Notion dados exportáveis** — Markdown export nativo
5. **Tudo self-host via Docker Compose (eventual)** — VM swap não corrompe stack

**Anti-pattern a evitar:** adoptar SaaS que só exporta para si mesmo (Bedrock AgentCore proprietary state, OpenAI Agent Builder schemas opaque).

---

## 🤔 What's uncertain (open questions)

1. **Paperclip vs Multica long-term winner**: ambos lançados Apr 2026 (novos). Pode ser que ambos morram e algo melhor emerja Q3.
2. **gbrain stability**: 12k★ mas Garry Tan move-fast. Pin versão concreta + backups disciplined.
3. **Soilytix Phase 3 reality**: o "8-agent department" é vision. Phase 2 (3-5 agents) pode descobrir que 4 chega. Não over-build.
4. **Migration cost xquads → SKILL.md**: estimei 30 min/squad, pode ser 2h se queres polish nível-gstack. Comece com 1, calibra.
5. **Notion as Layer 3 tem teto**: 3 req/s API rate limit vai partir aos 5+ agents. Pode forçar migration para Linear cedo.

---

## Cross-links

- Wiki: [`thin-harness-fat-skills`](thin-harness-fat-skills.md) — pattern arquitectural por trás
- Wiki: [`ai-native-company`](ai-native-company.md) — Diana YC framework (closed loops, queryable org)
- Wiki: [`20x-companies`](20x-companies.md) — case studies validating approach
- Wiki: [`tools/g-stack`](../tools/g-stack.md) — Garry Tan implementation
- Wiki: [`tech/REPORTING-AGENT-V3`](../tech/REPORTING-AGENT-V3.md) — concrete Phase 1 build spec
- Wiki: [`people/garry-tan/profile`](../people/garry-tan.md)
- Notion: [SOI-CONSTITUTION-00](https://www.notion.so/34bf18407014818481bae5bb6b164fea) — SDD adoption ticket
- Notion: [MKT-OPS-04 Reporting Agent V3](https://www.notion.so/34ef184070148143bce7ef69adb7cfb7) — build tracker

## Sources

- Conversation Cleiton ↔ Claude Code, Apr 28 2026 (deep planning session)
- Subagent research Apr 28 2026 — landscape Apr 2026 + free tier verification Perplexity
- Direct WebFetch paperclip.ing + multica.ai + cursor.com/pricing Apr 28 2026
- knowledge/tech/2026-04-28-* (3 YouTube videos: Garry Tan, Diana YC, "20X companies")
