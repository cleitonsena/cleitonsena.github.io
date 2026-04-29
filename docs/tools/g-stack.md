---
title: gstack
type: tool-reference
category: AI Engineering Frameworks
maintainer: Garry Tan (Y Combinator)
license: open-source
repo: https://github.com/garrytan/gstack
companion_repo: https://github.com/garrytan/gbrain
status: Active (Apr 2026, 85.8k+ stars rapidamente)
last_updated: '2026-04-28'
tags:
- claude-code
- ai-agents
- spec-driven-development
- skills
- engineering-process
sources:
- https://www.youtube.com/watch?v=wkv2ifxPpF8
- https://github.com/garrytan/gstack
---

# gstack

> Open-source set de **40+ skills/commands** que transforma Claude Code numa AI engineering team. Implementação do pattern **"Thin Harness, Fat Skills"** — scaffolding mínimo, skills especializadas que fazem o heavy lifting.
>
> **Repo oficial:** [`github.com/garrytan/gstack`](https://github.com/garrytan/gstack) (sem hífen)
> **Repo companheiro:** [`github.com/garrytan/gbrain`](https://github.com/garrytan/gbrain) — cross-machine memory layer

## TL;DR

Em vez de tentar tornar Claude Code mais inteligente via prompts complexos, gstack dá-lhe **uma equipa de especialistas** (skills) que ele invoca por etapa. Cada skill é o destilado de práticas reais — ex: `office-hours` é destilada de milhares de horas dos 16 partners YC fazendo office hours com fundadores.

## Skills inventariadas (40+) — selecção

### Discovery / ideação

| Skill | O que faz |
|---|---|
| **`/office-hours`** | YC Office Hours simulado. **6 forcing questions** em modo startup OU brainstorming em modo builder. Ver detalhe abaixo. |
| `/autoplan` | Pipeline completa: CEO + eng + design + DevX review com defaults do Garry. |
| `/plan-ceo-review` | Stage isolada de CEO review (estratégia + scope). |
| `/plan-eng-review` | Stage de engineering review (architecture + feasibility). |
| `/plan-design-review` | Stage de design review. |
| `/devex-review` | DevX-specific review. |
| `/cso` | "Chief Strategy Officer" — strategic decisions. |

### Design

| Skill | O que faz |
|---|---|
| `/design` | Design exploration. |
| `/design-shotgun` | Visual brainstorming — 3 variants em ~60s via OpenAI Codex (image gen). User pica vencedor. |
| `/design-html` | HTML-specific design generation. |
| `/design-consultation` | Consultative mode. |
| `/design-review` | Visual polish review. |

### Build

| Skill | O que faz |
|---|---|
| `/pair-agent` | Agent pairing para tasks complexas. |
| `/codex` | Codex-specific invocation (para "autistic CTO" debug heavy). |
| `/openclaw` | OpenClaw spawn — sub-sessions auto-orchestradas. |
| `/freeze` | Freeze a snapshot do estado. |
| `/careful` | Careful mode — extra adversarial review. |

### QA / Ship

| Skill | O que faz |
|---|---|
| `/browse` | Wrapped Playwright + Chromium CLI. Headed + headless. Substitui Chrome MCP. |
| `/qa` (não no listing root mas referenciado) | Regression test wrapping `/browse`. |
| `/canary` | Canary deployment / verification. |
| `/health` | Health check do projecto. |
| `/investigate` | Bug investigation. |
| `/learn` | Captura learnings em jsonl per-project. |
| `/document-release` | Release documentation. |
| `/land-and-deploy` | Final land + deploy. |
| `/landing-report` | Post-land report. |
| `/guard` | Pre-action safety guard. |
| `/benchmark` | Benchmark performance. |
| `/benchmark-models` | Compare model outputs. |
| `/context-save` | Save context (handoff). |
| `/context-restore` | Resume context next session. |

### Meta / housekeeping

| Skill | O que faz |
|---|---|
| `/gstack-upgrade` | Self-update gstack. |
| `/model-overlays` | Per-model behavioral patches (claude / codex). |
| `/extension` | Extension system. |

## A skill estrela: `/office-hours` — 6 Forcing Questions

Ver dump completo na [SKILL.md no repo](https://github.com/garrytan/gstack/blob/main/office-hours/SKILL.md). Resumo do core:

### Operating principles (não-negociáveis)
- **Specificity is the only currency** — "enterprises in healthcare" não é cliente
- **Interest is not demand** — waitlists, "interesting" não contam; **dinheiro conta, pânico quando partes contam**
- **The user's words beat the founder's pitch**
- **Watch, don't demo** — sentar atrás do user e morder a língua
- **The status quo is the real competitor** — não outras startups
- **Narrow beats wide, early**

### Anti-sycophancy rules
**NUNCA** dizer durante diagnóstico:
- "That's interesting"
- "There are many ways to think about this"
- "You might want to consider..."
- "That could work"
- "I can see why you'd think that"

**SEMPRE** tomar posição em cada resposta + dizer que evidência mudaria a tua opinião.

### As 6 forcing questions

| Q | Tema | Pergunta core | Push até ouvir |
|---|---|---|---|
| **Q1** | Demand Reality | "Strongest evidence someone wants this — não 'interested', não signed-up — alguém genuinamente furioso se desaparecer amanhã?" | Behaviour específico: pagamento, expansion, alguém que entra em pânico se cair |
| **Q2** | Status Quo | "What are users doing right now to solve this — even badly? What does workaround cost them?" | Workflow específico, hours, $$$, tools duct-taped |
| **Q3** | Desperate Specificity | "Name the actual human. Title. What gets them promoted? Fired? Keeps them up at night?" | Nome. Cargo. Consequência específica que ouviste da boca da pessoa |
| **Q4** | Narrowest Wedge | "Smallest possible version someone would pay real money for — esta semana, não depois do platform" | 1 feature. 1 workflow. Email semanal. Algo shippable em dias |
| **Q5** | Observation & Surprise | "Já te sentaste a ver alguém usar isto sem ajudar? What surprised you?" | Surpresa específica. User a fazer algo que contradiz tuas suposições |
| **Q6** | Future-Fit | "Se o mundo for diferente em 3 anos, o teu produto vira mais ou menos essencial?" | Claim específico sobre como o mundo dos users muda |

### Smart routing (não precisas das 6 sempre)
- **Pre-product** → Q1, Q2, Q3
- **Has users** → Q2, Q4, Q5
- **Has paying customers** → Q4, Q5, Q6
- **Pure engineering/infra** → Q2, Q4 only

### Pushback patterns (5 examples no repo)
1. Vague market → force specificity
2. Social proof → demand test
3. Platform vision → wedge challenge
4. Growth stats → vision test
5. Undefined terms → precision demand

---

## 🌱 Adapted for Soilytix — 3 candidate skills

Baseado em ler o office-hours real, eis **3 adaptations** para o nosso contexto GTM/operacional:

### `/icp-stress-test` (priority 1, May 2026)

Adapt office-hours para **Soilytix ICP discovery / re-validation** — estamos meio do ICP shift (Forestry/Gov vs Seed Co), forcing questions ajudam a calibrar:

| Q | Pergunta adaptada |
|---|---|
| Q1 | "Que evidência específica temos que [segmento] genuinamente precisa de soil intelligence — não 'interested in pilot', mas closed deals + return purchases?" |
| Q2 | "O que farmers/foresters [segmento] fazem hoje sem nós? Que workaround usam? Que custo (€/ha, hours/season) tem?" |
| Q3 | "Nome o decisor. Cargo. Que consequência ele sofre se solo dele continuar misurated?" |
| Q4 | "Qual é o menor ticket que um cliente [segmento] paga ESTA SEMANA? €1k single-sample? Subscription Soil Essentials?" |
| Q5 | "Sentaste-te com 1 cliente a interpretar relatório? Que pergunta surpreendente fez?" |
| Q6 | "EUDR + Soil Monitoring Law em 3 anos torna soil microbiome platform mais ou menos essencial? Por quê?" |

**Output:** ICP fitness score 0-10 + assignment concreto (ex: "interview 3 Coillte foresters next week").

### `/blog-icp-fit-check` (priority 2, May)

Adapt para **gating de blog content antes de Franziska review**, usando lessons da feedback dela 27 Apr ("AI-written, fundamental inaccuracies").

```
Mode: technical-content review
Persona: skeptical agronomy/microbiology reviewer (Matthias proxy)

Forcing questions:
Q1 — Specificity: "Cada claim científico tem citation? Or framing 'studies suggest'?"
Q2 — Anti-fabrication: "Há números? Onde vêm? São do nosso LUCAS dataset n=11,338 ou inventados?"
Q3 — Audience match: "Que agronomista 50-500ha lê isto e pensa 'isto não é para mim'?"
Q4 — Wedge: "Qual é o ONE takeaway prático que o leitor faz different segunda-feira?"
Q5 — AI tells: "Frases como 'In today's world', 'crucial', 'comprehensive', 'fundamental'? Replace."
Q6 — Differentiator: "Que post existente no Soil Health Tools / DSMZ blog cobre isto melhor? Por que ler nosso?"

Output: Pass/fail + line-by-line edits + score 0-10.
```

Bate com a feedback estrutural do Franzi: **science-team-first**, antes de Cleiton publicar nada.

### `/cold-email-pushback` (priority 3, May)

Adapt para **adversarial review de outreach copy** antes de bombardear prospects — protege a nossa Resend reputation + previne queimar contactos cold como o W3 LinkedIn.

```
Mode: cold-email red-team
Persona: skeptical busy decision-maker (Alessia Lenders proxy)

Forcing questions:
Q1 — Trigger reason: "Why now? Por que mando isto a esta pessoa hoje, não daqui a 6 meses?"
Q2 — Status quo: "O que ela está a usar? Por que isto é melhor que ignorar?"
Q3 — Specific consequence: "Que dor específica ela tem que isto resolve? Não 'soil intelligence' — pain real."
Q4 — Wedge: "Qual é o pedido smallest? 15 min call? Send 1 sample? Não 'meet to discuss'."
Q5 — Anti-pattern: "Há 'I hope this finds you well'? 'Quick question'? 'Just checking in'? Replace ou cut."
Q6 — Reply test: "Se ela responder com 1 frase 'sounds good', qual é o follow-up automático?"

Output: Edit suggestions + pass/fail + reply-rate prediction.
```

**Validation:** Apply estes a 5 BD prospects da pipeline antes de Tue first-LIVE send. Se >= 4/5 pass, ship; senão revise.

---

## Conductor — multi-worktree manager

Tool separada (mesmo ecosystem). Permite correr **10-15 sessões Claude Code em paralelo** cross multiple projects. 3-4 sessões podem estar no mesmo project, em parallel branches. Click `+` cria nova worktree para nova ideia/bug/feature.

Built-in support para gstack (clica gstack ao iniciar, dropa direto em office-hours).

## Comparação com nosso stack actual

| Aspecto | Soilytix actual | gstack |
|---|---|---|
| Spec/process framework | BMAD + GSD + Spec Kit (parcial) | gstack 40+ skills |
| Multi-session orchestration | Manual (várias terminais Claude Code abertas) | Conductor (worktree-aware) |
| Browser automation | Não temos ainda | `/browse` + `/qa` Playwright wrap |
| Adversarial review | Manual (Cleiton revê) | Skill explícita `/careful` + 6 forcing Qs |
| Cross-machine memory | Notion + auto-memory `~/.claude/` | gbrain (private repo + sync) |

## Considerações para adoptar

**Pros:**
- Pattern "Thin Harness, Fat Skills" valida o que estamos a fazer com SDD
- 3 candidate skills above (icp-stress-test, blog-icp-fit-check, cold-email-pushback) são **production-ready adaptations** a partir do que li
- Office-hours anti-sycophancy rules **diretamente aplicáveis** a Cleiton ↔ Claude (Cleiton tende a aceitar respostas optimistas demais)

**Cons:**
- Foco do Garry é software dev — adapt para nosso GTM/operacional precisa trabalho
- gstack tem MUITO plumbing (telemetry, gbrain sync, upgrade prompts) — adopting full = noise
- Stack já cheia (BMAD, GSD, Spec Kit) — risco de proliferation

**Recomendação revista (Apr 28):**
1. **Adoptar pattern + writing voice** (anti-sycophancy + "Voice" rules em `../tools/g-stack.md` SKILL.md preamble) — copy SAS-CC instructions
2. **Construir 1 skill nossa**: `/icp-stress-test` em May, baseada no template de office-hours mas Soilytix-specific
3. **NÃO adoptar full gstack vendoring** — too much plumbing
4. **Conductor para parallel sessions**: revisitar depois Reporting Agent V3 ship

## Acção concreta para Cleiton (TODO)

- [x] Verificar repo handle → confirmed `garrytan/gstack`
- [x] Ler `office-hours/SKILL.md` (lido + síntese acima)
- [ ] Aplicar **anti-sycophancy rules** ao SOI-CONSTITUTION (tickets descrevem isto?)
- [ ] May 2026: implementar `/icp-stress-test` baseado no template office-hours

## Cross-links

- Pessoa: [`../people/garry-tan.md`](../people/garry-tan.md)
- Concept: [`../concepts/thin-harness-fat-skills.md`](../concepts/thin-harness-fat-skills.md)
- Concept relacionado: [`../concepts/ai-native-company.md`](../concepts/ai-native-company.md)
- Concept relacionado: [`../concepts/20x-companies.md`](../concepts/20x-companies.md)
- Related tool: [`https://docs.claude.com/en/docs/claude-code/overview`](claude-code.md)
- SDD context: Notion [SOI-CONSTITUTION-00](https://www.notion.so/34bf18407014818481bae5bb6b164fea)

## Sources

- [YouTube — Garry Tan, Apr 2026](https://www.youtube.com/watch?v=wkv2ifxPpF8) — transcript: `knowledge/tech/2026-04-28-how-to-make-claude-code-your-ai-engineering-team.md`
- [gstack repo](https://github.com/garrytan/gstack) — explored Apr 28
- [gstack/office-hours/SKILL.md](https://github.com/garrytan/gstack/blob/main/office-hours/SKILL.md) — 6 forcing questions canonical reference
