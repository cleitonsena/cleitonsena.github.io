---
name: Garry Tan
category: AI Engineering & Startup Methodology
role: President & CEO, Y Combinator
platforms:
- twitter
- youtube
- github
twitter: '@garrytan'
youtube: '@garrytan'
github: garrytan
last_updated: '2026-04-28'
tags:
- ai
- claude-code
- ai-agents
- engineering-process
- startup-methodology
- spec-driven-development
sources:
- https://www.youtube.com/watch?v=wkv2ifxPpF8
---

# Garry Tan

> Engenheiro de carreira, co-fundador Posterous (vendido ao Twitter), employee #10 Palantir, agora CEO/President de Y Combinator. Em Abril 2026 publicou **gstack** — open-source repo ([github.com/garrytan/gstack](https://github.com/garrytan/gstack), **85.8k+ stars** rapidamente, mais que Ruby on Rails) que codifica o que aprendeu construir com Claude Code + Codex em paralelo. Repo companheiro: [`gbrain`](https://github.com/garrytan/gbrain) (12k stars) — "Garry's Opinionated OpenClaw/Hermes Agent Brain" para cross-machine memory.

## Quem é

- **Stanford** — Computer Systems Engineering
- **Palantir** — engineer #10, designer + PM simultaneamente
- **Posterous** — co-founder. Construído ao longo de 2 anos com 10 engenheiros + $10M. Vendido à Twitter.
- **Y Combinator** — fez office hours com milhares de startups. Usa essa experiência destilada como base do G-Stack.
- **Bookface** — primeira versão do social network interno + knowledge base do YC.

## Tese central (Abril 2026)

> *"A forma de pôr agents a fazer trabalho real é exactamente como humanos sempre o fizeram — em equipa, com papéis, com processo, com revisão."*

Estamos numa nova era de building software — **agent era**. O bottleneck **não é a inteligência do modelo**: os modelos já são suficientemente inteligentes. O bottleneck é o **scaffolding** que os rodeia. Out-of-the-box o modelo "wanders" — gera código plausível que silently breaks.

A solução: **scaffolding deve ser trivialmente fino + skills devem ser gordas**. É a sua filosofia "Thin Harness, Fat Skills" — ver [`../concepts/thin-harness-fat-skills.md`](../../concepts/thin-harness-fat-skills.md).

## Stat marcante

> *"Codei mais nos últimos 2 meses que em todo o 2013, que foi a última vez que trabalhei a sério como engenheiro. Reconstruí essencialmente todo o Posterous — que demorou 2 anos, $10M e 10 engenheiros — em 2 meses, sozinho."*

## Workflow real do Gary (Abr 2026)

- **10-15 sessões Claude Code paralelas** simultaneamente, cross multiple projects
- Sometimes 3-4 sessões no mesmo projecto, parallel branches, parallel PRs
- "**Já não tem to-do list**" — quando lhe vem uma ideia ou bug report, click `+` no Conductor → cria worktree → corre office hours → CEO review → engineering review → adversarial review → ship
- 10-50 PRs/dia dependendo das meetings
- Tem múltiplos open-source projects com tens of thousands of stars; a sit em ~400 PRs por reviewar a qualquer momento

## Modelos que prefere (e como pensa neles)

> *"Claude Code by default usa Claude — Opus 4.6 é tipo um CEO ADHD. É o gajo com quem queres ir tomar uma cerveja, tem mil ideias. Mas quando a coisa fica difícil, chamas o autistic CTO — esse é o Codex (OpenAI)."*

| Modelo | Personalidade na metáfora dele | Quando usar |
|---|---|---|
| **Claude Opus 4.6** | "ADHD CEO" — cerveja friend, mil ideias | Default Claude Code, ideação, review estratégico |
| **OpenAI Codex** | "Autistic CTO" — heads-down, debugging profundo | Bug-hunting, sorting out crazy bugs, image generation (design shotgun) |

## Por que importa para Soilytix

1. **Validação da abordagem SDD/process-first** que adoptámos (Spec Kit + BMAD + GSD). Gary usa pattern muito semelhante: gates explícitos, evidence-based transitions, multi-stage adversarial review.
2. **Office Hours skill** — pode ser adaptada para nosso ICP discovery + GTM ideation work (não só product).
3. **Parallel Claude Code sessions** — relevante para escalarmos pipeline development para 4+ workflows sem virar bottleneck.
4. **Browser automation via wrapped Playwright (não via Chrome MCP)** — Gary diz Chrome MCP é "one of the worst pieces of software I've ever used". Pode informar futuras decisões nossas (e.g., scraping competitivo, screenshot automation).
5. **70k stars em 3 semanas** = sinal forte de market fit em AI engineering tooling. Vale acompanhar para ver onde a categoria evolui.

## Citações worth keeping

> *"It turns out the way to get agents to do real work is the same way humans have always done it — as a team with roles, with process, with review."*

> *"The bottleneck is not the model's intelligence. As long as you set the models up right, they are already smart enough to do extraordinary work on your code base."*

> *"The scaffolding should be trivially thin."*

> *"Cloud is just someone else's computer."* — argumentando para automação local (browser) em vez de cloud.

> *"You're able to have an idea and then get farther along with it than you ever would be."*

> *"This is the most incredible time in history to build software, the barrier to building just collapsed. The only question left is what are you gonna build? Go make something people want."*

## Cross-links

- Tool: [`../tools/g-stack.md`](../../tools/g-stack.md)
- Concept: [`../concepts/thin-harness-fat-skills.md`](../../concepts/thin-harness-fat-skills.md)
- Concept relacionado: [`../concepts/ai-native-company.md`](../../concepts/ai-native-company.md) — Diana (YC) framework de closed-loop org
- Concept relacionado: [`../concepts/20x-companies.md`](../../concepts/20x-companies.md) — pattern de internal automation (GigaML/Legion/FaceShift)
- Related people: [`https://karpathy.ai`](https://karpathy.ai) (também advoga "tools + skills > raw model intelligence")
- Soilytix application: [`../tech/reporting-agent-v3.md`](../../tech/REPORTING-AGENT-V3.md) (Phase 2 — explorar parallel agent sessions per Gary pattern)
- SDD adoption: Notion ticket [SOI-CONSTITUTION-00](https://www.notion.so/34bf18407014818481bae5bb6b164fea)

## Sources

- [YouTube — How to Make Claude Code Your AI Engineering Team (Abr 2026)](https://www.youtube.com/watch?v=wkv2ifxPpF8) — transcript em `knowledge/tech/2026-04-28-how-to-make-claude-code-your-ai-engineering-team.md`
- [G-Stack repo](https://github.com/garrytan/g-stack)
