# Skill Check

> An audit gate for agent Skills — full audits, idea evaluation, and instability troubleshooting, driven by the 6 Essentials & 6 Steps model.

Skill Check turns "is this skill any good?" into a repeatable audit instead of a gut feeling. Point it at any `SKILL.md` (or a whole skill directory) and it produces a structured audit report: a four-level verdict, an item-by-item checklist with quoted evidence, P0/P1/P2 graded issues, and before→after fix suggestions. It also fires **at creation time** — audit a new skill's first draft before it ever ships — and it can troubleshoot a working skill whose output drifts.

## What it solves

- **Quality drift** — a skill that works today and falls apart tomorrow, because its examples and validation rules were never written down.
- **Experience stuck in one head** — good prompting practice that never gets packaged, so nobody else can reuse it.
- **No shared baseline** — every teammate writes skills their own way, with no common definition of "good".
- **Premature packaging** — turning a one-off task into a skill is wasted effort; Skill Check tells you before you invest.

## The audit model: 6 Essentials & 6 Steps

**The 6 Essentials are the audit standard — all required. The 6 Steps are the audit process.** Six quality traits (*Focus · Gate · Proof · Reuse · Share · Change*) provide the advanced assessment. Motto: *complete essentials, steady traits.*

**The standard — 6 Essentials**

| # | Essential | What "good" looks like |
|---|---|---|
| 1 | Trigger name | Verb + object, with concrete trigger phrases in the description — recognizable at a glance |
| 2 | Input requirements | Required / optional / format stated precisely — no "just give me the stuff" |
| 3 | Instructions | Numbered, executable steps; edge-case rules stated explicitly, not implied |
| 4 | Output format | A structured, field-level contract with an empty-section fallback ("无") |
| 5 | Examples | Good input → good output, plus a negative example to avoid — the biggest lever for stability |
| 6 | Validation rules | A checklist of red lines the output must pass before shipping — more is better |

**The process — 6 Steps**

1. **Locate & read** — frontmatter, directory structure, bundled resources verified to exist and be referenced.
2. **Premise check** — worth a skill? (repeated 3+ times / team baseline / experience inheritance) or too early (one-off / exploratory / always different).
3. **6 Essentials audit** — each rated ✅ / ⚠️ / ❌ with the skill's own text quoted as evidence.
4. **Quality traits** — Focus / Gate / Proof / Reuse / Share / Change, each with reasoning.
5. **Audit report** — P0/P1/P2 issues, each with a before→after fix. Criticism without a fix = failed report.
6. **Re-audit** — fix → re-check; expect convergence in 3-5 rounds. For instability, check **examples first, validation rules second**.

**Three scenarios**: full audit (default — including first drafts at creation time) · idea evaluation (worth packaging / too early / need more info) · instability troubleshooting (symptom → missing essential → fix).

**Asking vs. assuming**: Skill Check does not interrogate. It asks only when (a) no skill content was provided, (b) troubleshooting lacks a real failure sample, or (c) a fix involves trade-offs — max 3 questions, each with a default assumption and a degradation path.

## Case 1 — At creation: block, fix, release, then elevate

Audits happen at two moments — **at creation** (gate a first draft before it ships) and **in use** (troubleshoot a live skill that drifts). This case covers the first; Case 2 covers the second.

A first-draft skill arrives for creation-time audit:

```markdown
---
name: data-helper
description: 帮你处理数据
---
把数据发给我，我帮你分析得清晰美观。
```

**Verdict: Fail — blocked.** 5 of 6 Essentials missing. Every P0 ships with a before→after fix:

| P0 issue | Before (quoted) | After (fix) |
|---|---|---|
| Dead trigger | `name: data-helper`, "帮你处理数据" | `name: data-weekly-summary` + description with concrete trigger phrases ("汇总周报、周报合并、weekly summary") |
| No input contract | "把数据发给我" | Required: 2+ weekly reports (CSV paths or pasted tables); optional: aggregation scope; >5000 rows → confirm sampling first |
| Non-executable instructions | "我帮你分析" | 1. Read all reports, flag column mismatches; 2. Merge into one master table; 3. Mark report-specific metrics; 4. Output the summary |
| Unverifiable output | "清晰美观" | Fixed 3 sections: master table / discrepancies ("无" if none) / unique metrics per report |
| No examples | — | Add one complete pair: two 10-row sample reports → the full expected summary |

**P1**: add a negative example (e.g. "missed a discrepancy flag") and 3+ validation rules ("all sections present, missing ones output 无; every input report included; raw numbers untouched").

User applies the fixes → re-audit → **Pass, gate opens**. One block + one re-check = converged in 2 rounds.

**Then one elevation round (Pass → Excellent).** Release is not the finish line; the audit closes with grounded P2 suggestions, each tied to a concrete essential:

1. Add a boundary sample (an over-5000-row report) to prove the sampling-confirmation rule actually fires — strengthens *Gate*.
2. Collect the team's slang name for the task ("并周报") into the trigger phrases — strengthens *Trigger name*.
3. Turn this draft's own P0s into an author self-check ("before submitting any new skill, verify all 6 Essentials") — same mistakes never recur.

User adopts → V2 re-audit → **Excellent**. V1 works, V2 works well — one step per round, every step executable.

## Case 2 — In use: ask, locate, fix, then harden

**User**: "My weekly-summary skill is flaky." No run sample provided.

**Skill Check asks** (rule: no sample → ask, max 3 questions, with a degradation path):

> 1. What input did you feed it last time the output was bad?
> 2. What did the output actually look like?
> 3. What did you expect instead?
> (No sample available? I'll run a static audit and mark the report "no dynamic troubleshooting".)

**User replies**: "Pasted three reports; the 'discrepancies' section appears sometimes, vanishes other times; I expect every section to always show, with 无 when empty."

**Root cause located**: the output format never specified an empty-section fallback (Essential 4), and the validation checklist never verified section completeness (Essential 6). **Fix**: add the "missing section → output 无" rule to both the format spec and the validation checklist, plus one no-discrepancy example to the sample library. Every answer from the user lands directly in the diagnosis — no question is wasted.

**Then harden it (one step beyond the fix).** The failure sample itself joins the example library as a negative example with its cause annotated, and the team adopts a habit: every real-world failure gets brought back into the audit, becoming an example or a validation rule. The skill's path: **V1 runs → V2 stops drifting → V3 stays stable** — each problem happens only once.

## Case 3 — Idea evaluation

- "I merge three data reports into one format every week — done it 5 times" → **Worth packaging** (repeated 3+ times + team baseline).
- "A one-off special report for tomorrow's client visit" → **Too early** — premature packaging = wasted time.
- "Maybe a writing skill?" → **Need more info** — Skill Check asks: how many times last month? (default assumption: ≥3 → worth it). User: "Once." → **Too early**; collect two more similar runs, then re-evaluate.

## Output: the audit report

Plain Markdown, verdict first: **verdict** (Excellent / Pass / Needs fixes / Fail — with block-or-release action) → **checklist with quoted evidence** → **P0/P1/P2 issues with before→after fixes** → **fix suggestions** → **root-cause section** (troubleshooting only) → **self-check confirmation**.

## Installation

**Requirements**: an agent that supports SKILL.md-based skills (WorkBuddy, Claude Code, CodeBuddy, Codex, TRAE, Qoder, …).

**Get the skill**: unzip `awesome-skill-check.zip`, or clone this repository, then copy the `awesome-skill-check/` folder into your agent's skills directory:

| Agent | Skills directory |
|---|---|
| WorkBuddy | `~/.workbuddy/skills/` |
| Claude Code | `~/.claude/skills/` |
| CodeBuddy | `~/.codebuddy/skills/` |
| Codex | `~/.codex/skills/` |
| TRAE | `~/.trae/skills/` |
| Qoder | `~/.qoder/skills/` |

Restart or reload the agent; the skill triggers from natural language.

## Usage triggers

- **Audit**: "review this skill" / "audit my skill" / "检查 skill" / "评审 skill" / "审核 skill"
- **Creation-time**: "创建一个 skill" / "新建 skill" / "帮我写个 skill" — the audit fires on the first draft, before use or release
- **Evaluation**: "is this idea worth a skill?" / "值不值得做成 skill"
- **Troubleshooting**: "my skill output is unstable" / "skill 不稳定" / "skill 不好用、输出飘忽"

## FAQ

- **Too many skills to manage?** Keep a personal inventory; team skills go in a shared library. Periodic audits keep the library healthy.
- **Can I use a skill someone else defined?** Check its permission/scope first; reuse is encouraged in principle. Audit before adopting.
- **Unstable output — where first?** Examples (too thin) and validation rules (missing) cause most drift.

## Repository layout

```
awesome-skill-check/
├── SKILL.md                          # audit workflow, input & asking rules, report template, validation red lines
├── references/
│   ├── quality-standards.md          # grading rules for the 6 Essentials & quality traits, with good/bad examples
│   └── review-examples.md            # full audit playbooks: blocking a broken skill, the asking process, good vs. bad reports
├── README.md
└── LICENSE
```

## License

Copyright © 2026 Ethanjhli. All rights reserved. See [LICENSE](LICENSE).
