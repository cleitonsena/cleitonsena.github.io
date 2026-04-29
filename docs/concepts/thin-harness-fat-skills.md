---
title: Thin Harness, Fat Skills
type: concept
category: AI Engineering Patterns
coined_by: Garry Tan (Y Combinator, Apr 2026)
last_updated: '2026-04-28'
tags:
- ai-agents
- claude-code
- engineering-process
- skills
- scaffolding
sources:
- https://www.youtube.com/watch?v=wkv2ifxPpF8
---

# Thin Harness, Fat Skills

> Pattern arquitectural para AI engineering teams: o **harness** (scaffolding em volta do modelo) deve ser trivialmente fino. As **skills** (especialistas que o modelo invoca) devem ser gordas, opinativas, e capturar wisdom destilada.

## O problema que resolve

LLMs out-of-the-box "wandeiam":
- Não conhecem os teus dados → adivinham
- Adivinhar à escala = código plausível que silently breaks
- Escalar com mais prompt-engineering raro funciona — ganha-se complexidade sem ganhar fiabilidade

A reação natural é construir scaffolding pesado em volta do modelo (chains, agents, validators, loops, retries) — Gary chama a isto **"backwards"**:

> *"The bottleneck is not the model's intelligence. As long as you set the models up right, they are already smart enough to do extraordinary work on your code base. This is backwards. The scaffolding should be trivially thin."*

## A inversão

| Anti-pattern (Heavy harness, thin skills) | Pattern (Thin harness, fat skills) |
|---|---|
| Workflow engine complexo orquestra steps | Harness é só "qual skill chamo a seguir?" |
| Cada step é um prompt curto + parsing | Cada skill é um documento opinativo, opinionated, com persona |
| Lógica vive em código (Python/Node) que envolve modelos | Lógica vive em markdown que o modelo lê e age sobre |
| Adicionar capability = code change + deploy | Adicionar capability = novo file `.md` |
| Hard a debugar — bugs estão entre system prompts | Debugar é ler um markdown |

## Formula prática

```
Thin Harness = (Conductor / Claude Code CLI / shell)
            + (skill-router: invocar /command apropriado)
            + (multi-worktree para parallelization)

Fat Skills   = markdown files com:
            ├─ Persona (quem o modelo "é" durante esta skill)
            ├─ Forcing questions (perguntas que o modelo DEVE fazer)
            ├─ Decision criteria (quando avançar vs voltar)
            ├─ Domain knowledge (e.g., YC partner reasoning)
            ├─ Adversarial checks (auto-fix de premises fracas)
            └─ Exit conditions (deliverable claro)
```

Exemplo concreto: a skill `office-hours` do G-Stack destila milhares de horas dos 16 partners YC fazendo office hours com fundadores. Não é código — é um documento opinativo. O modelo LÊ e age como faria um partner YC.

## Por que funciona

1. **LLMs já são suficientemente inteligentes**. O que falta é **direção e contexto**, que vivem nas skills.
2. **Skills são versionáveis em markdown** — git-trackable, peer-reviewable, refinable iterativamente sem deploys.
3. **Specialização compõe** — cada skill faz UMA coisa bem. Compor 5 skills numa pipeline (office hours → CEO review → eng review → ...) cobre a complexidade real.
4. **Wisdom não-óbvio** (ex: "que perguntas perguntar primeiro a um founder") é o que os modelos não sabem por treino. Encapsulado em skills, fica disponível.

## Anti-patterns para evitar

- **Mega-prompt** — tentar meter "tudo" num system prompt gigante. Modelos perdem foco. Quebrar em skills.
- **Chain-of-thought hardcoded** — fixar a sequência em código. Skills devem decidir dinamicamente o que vem a seguir.
- **Validator-everywhere** — espalhar validators no harness em vez de embedar critérios na própria skill.
- **Re-implementar Claude Code** — se já existe uma CLI/IDE com skill support, usa-o. Não reconstruir.

## Aplicação a Soilytix

### Onde já fazemos algo parecido (sem o nome)

- **BMAD** instalado — workflow engine com gates. Mais perto de "heavy harness".
- **GSD (`/gsd:plan-phase`, `/gsd:execute-phase`)** — skills explícitas que se compõem. **Já é Thin Harness, Fat Skills no software stack.**
- **Spec Kit / SOI-CONSTITUTION** (Backlog) — pretende ser thin harness com gates G0-G5; cada gate é uma skill de validação.

### Onde podemos aplicar (próximas semanas)

| Domínio | Skill candidata | Replaces |
|---|---|---|
| **Blog content** | `/blog-icp-fit-check` (review-gate adaptado de `office-hours`) | Bloqueio actual com Franziska/Matthias review manual |
| **Outreach copy** | `/cold-email-pushback` (adversarial review da copy antes do envio) | Redação ad-hoc do Cleiton |
| **GTM ideation** | `/icp-stress-test` (Russell Brunson Dream 100 + Lochhead category framing) | Discussion documents |
| **Deal qualification** | `/closed-won-pattern-check` (compara deal com pattern Apr 2 dataset) | Gut-feel scoring |

### Onde NÃO aplicar (yet)

- Pipelines GHA (BD, PR, Blog, Cockpit) — esses são **automação determinística**, não AI agents. Skills aplicam-se à parte criativa/decisional, não ao Python que faz HTTP requests.

## Cross-links

- Pessoa: [`../people/garry-tan.md`](../people/garry-tan.md)
- Tool implementation: [`../tools/g-stack.md`](../tools/g-stack.md)
- Related concept: [`https://docs.claude.com/en/docs/claude-code/overview`](https://docs.claude.com/en/docs/claude-code/overview)
- Concept complementar (org-level): [`ai-native-company`](ai-native-company.md) — Diana YC framework. Thin-Harness/Fat-Skills é o pattern de implementação dentro do paradigma AI-native company.
- Concept complementar (case studies): [`20x-companies`](20x-companies.md) — empresas reais a aplicar este pattern (GigaML/Atlas é exemplo concreto de "fat skill"; FaceShift custom-agent-per-employee é literalmente skill-per-role)
- SDD context: Notion [SOI-CONSTITUTION-00](https://www.notion.so/34bf18407014818481bae5bb6b164fea)
- Related thinker: Andrej Karpathy ([`https://karpathy.ai`](https://karpathy.ai)) também advoga por "tools + skills > raw model intelligence" mas via Obsidian + Claude Code knowledge crunching workflow

## Sources

- [Garry Tan, Apr 2026 — How to Make Claude Code Your AI Engineering Team](https://www.youtube.com/watch?v=wkv2ifxPpF8)
- Transcript: `knowledge/tech/2026-04-28-how-to-make-claude-code-your-ai-engineering-team.md`
