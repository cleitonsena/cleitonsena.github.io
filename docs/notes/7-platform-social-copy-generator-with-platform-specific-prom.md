---
title: 7-Platform Social Copy Generator with Platform-Specific Prompts
updated: 2026-05-07
sources:
  - https://n8n.io/
  - https://www.anthropic.com/claude
tags: [n8n, content-automation, copywriting, multi-platform, build-in-public]
---

# 7-Platform Social Copy Generator with Platform-Specific Prompts

A refactored n8n workflow that transforms a single spreadsheet entry into seven platform-tailored posts, each respecting distinct voice rules, format constraints, and disclosure levels. One row in Sheet1 becomes a LinkedIn Post, LinkedIn Article, X thread, Bluesky post, Dev.to entry, Substack essay, and Wiki page—without manual rewrites.

The core insight: trailers (Posts/X/Bluesky) withhold mechanism details that only appear in long-form deep-dives (Article/Dev.to/Substack/Wiki). Audience segmentation by platform format enforces content hierarchy.

## Why it matters

Manual copy adaptation across seven platforms costs 30 minutes per entry. Teams either ship one generic post across all platforms (noise) or hand-write each variant (time tax). This workflow collapses that friction.

Platform-specific rules aren't optional flavor—they're structural. X's 300-character limit demands different syntax than Substack's 2,000-word essay. Dev.to readers expect code. Wiki readers expect principles and tradeoffs. Pretending these audiences are identical is how good ideas become seven bad posts.

The workflow enforces a content philosophy: reveal strategy in trailers, reveal mechanism only in destinations built for depth.

## The principle

Each platform has a **voice contract** and a **disclosure tier**.

**Voice Contracts:**
- **LinkedIn Post:** Conversational, status-oriented. Loser/winner contrast. No jargon. Hook in line 1. Calls reader identity into question.
- **LinkedIn Article:** Framework-heavy. Concrete examples. No code. Mechanisms explained step-by-step. Audience: practitioners skeptical of hype.
- **X / Bluesky:** Atomic. Opinions over summary. Constraint as clarity. No explanations—reactions only. Assume audience scrolling at 2x speed.
- **Dev.to:** Code-first. Executable examples. Working repository links. Assume reader wants to copy and modify. Pedagogical, not polemical.
- **Substack:** Narrative arc. Principles over procedures. Personal reasoning visible. Assumes deep reader willing to sit with ambiguity.
- **Wiki:** Encyclopedic with point-of-view. Principle, tradeoffs, connections. Timeless framing. Sources cited. Assumes reader building a mental model, not seeking urgency.

**Disclosure Tiers:**
- **Tier 1 (Trailer):** What + Why only. No mechanism. Bait curiosity.
- **Tier 2 (Destination):** What + Why + How. Mechanism exposed. Reader compensates with time investment.

Trailers are Posts, X, Bluesky. Destinations are Article, Dev.to, Substack, Wiki. This separation prevents the curse of the generic post—the thing that sounds professional but moves no one.

## The workflow structure

The n8n graph accepts a Sheet1 entry with these required fields:
- `headline` — core claim or insight
- `context` — why this matters now
- `mechanism` — how it works (optional, for long-form only)
- `example` — concrete case or number
- `voice_tone` — target persona (Cleiton's typical: builders, not C-level)

The workflow branches into seven parallel Claude Haiku calls, each with a prompt template:

**LinkedIn Post Template:** "Write a 200-280 character Post. Hook in line 1. No jargon. Status contrast. Banned words: [list]. Audience: GTM operators."

**LinkedIn Article Template:** "1,200-1,500 words. Framework with 3-4 concrete steps. No code blocks. Mechanism explained. Audience: skeptical practitioners. Include a 'Tradeoffs' section."

**X Template:** "Thread, 3-5 tweets. Atomic claims. Opinions only. No explanations. Assume reader scrolling at 2x speed. No hashtags."

**Bluesky Template:** "Single post, 256 chars max. Status-oriented. Takes a position. No explanation. Conversational."

**Dev.to Template:** "1,500-2,500 words. Code-first. Working example. Repository link. Pedagogical tone. Assume reader will copy and modify."

**Substack Template:** "2,000-3,000 words. Narrative arc. Principle-driven. Show your reasoning. Personal voice. Timeless framing."

**Wiki Template:** "400-1,500 words. YAML frontmatter. Encyclopedic. Principle + tradeoffs + connections. Cross-link to related Wiki entries. Cite sources. Timeless."

Each call returns a formatted string. A final step assembles all seven into a single Sheet1 output row or separate rows per platform.

## Tradeoffs and when NOT to use

**This works for:**
- Repeated content pillars where voice rules are stable.
- Build-in-public operators shipping weekly or bi-weekly.
- Teams where one person owns multiple platforms (eliminates rewrite tax).
- Insights that have both quick takes (Posts) and deep dives (Essays/Wiki).

**This breaks when:**
- Platform rules change rapidly (X algorithm shifts, Bluesky formatting evolves).
- Your headline doesn't decompose into seven distinct angles (some ideas are just tweets).
- You need real-time responsiveness (n8n execution adds 2-3 minute latency).
- Your team prefers platform-native drafting over structured input (muscle memory vs. abstraction).

**Common failure modes:**
- Generic prompts produce generic output. Prompt tuning is iterative. Your first run will sound like Claude. Refine voice rules per platform.
- Over-stuffing headlines with mechanism. The workflow respects your input quality. Garbage in, garbage out.
- Forgetting that platforms are audience segments. Not all readers want Dev.to code. Not all want Substack essays. The branching assumes you reach different people on different platforms.

## Connections

[[wiki/content-stack]] — The broader philosophy of separating trailers from destinations.

[[wiki/voice-contracts]] — How to define platform-specific voice rules that stick.

[[wiki/build-in-public]] — Why shipping across seven platforms forces thinking clarity.

[[wiki/n8n-automation]] — Workflow architecture for content generation at scale.

[[wiki/disclosure-tiers]] — The principle of hiding mechanism in destinations, not trailers.

## Sources

[1] n8n Documentation. https://n8n.io/

[2] Anthropic. Claude API Reference. https://www.anthropic.com/claude

[3] Karpathy, A. (2023). A Scientist's Guide to Writing. https://github.com/karpathy/

[4] Cleiton Sena. Fractional Head of Revenue at Soilytix. Personal notes on multi-platform content architecture. Internal.