# AI team blueprint, version one

A three-agent research team that turns a question into a cited, independently reviewed brief. It runs inside Claude Code from this repo.

## 1. Use case

| Option | Value in week one | Risk if an agent is wrong | Review time needed | Extra setup |
|---|---|---|---|---|
| **Research briefs** | High | Low: nothing leaves the repo | Low: read one page | None |
| Content research and writing | Medium | Medium: published under your name | High: voice and accuracy edits | Publishing channel |
| Lead generation | Medium | High: outbound messages to real people | High: approve every send | CRM, email, data source |
| Customer support | Low without existing ticket volume | High: customer-facing | High | Helpdesk integration |
| Internal operations | Depends on existing processes | Medium | Medium | Access to internal systems |
| Personal productivity | Low: one agent does this, no team needed | Low | Low | Calendar and email access |

**Recommendation: research briefs.** With under 2 hours a week, the team has to run without supervision and the review has to be short. Research is the only option where nothing is sent, published, or spent, so the single approval point is you reading the result. It also produces the evidence files that a content or lead-generation team would need later.

This choice assumes no specific domain. Any question that can be answered from public web sources works.

## 2. Team structure

| Agent | Owns | Receives | Produces | Hands off when |
|---|---|---|---|---|
| Orchestrator (the main Claude Code session, via `/brief`) | Scope, routing, the final report | Your question | Scope note, log row, summary | Each agent reports done |
| Researcher | Evidence | Question, scope note | `evidence.md` | Every sub-question has 2 sources, or 12 sources reached |
| Writer | The brief | `evidence.md` | `brief.md` (600 words maximum) | Every factual sentence is cited |
| Reviewer | The quality gate | `brief.md`, `evidence.md` | `review.md` with PASS or REVISE | The claim table covers every cited sentence |

How overlap is prevented:

- Each agent writes exactly one file.
- The Writer has no web tools, so it cannot add facts.
- The Reviewer cannot edit the brief, so it cannot quietly fix what it should report.
- The Researcher does not give opinions or pick between conflicting sources.

## 3. Agent prompts

The production prompts are the agent files themselves:

- `.claude/agents/researcher.md`
- `.claude/agents/writer.md`
- `.claude/agents/reviewer.md`
- `.claude/skills/brief/SKILL.md` (orchestration)

Each one contains role, input, output format, rules, escalation conditions, and a done condition. Edit the file to change the agent.

## 4. Workflow

```
You: /brief <question>
  |
  v
Orchestrator: scope note, create briefs/<slug>/
  |
  v
Researcher --> evidence.md
  |
  v
Writer -----> brief.md
  |
  v
Reviewer ---> review.md
  |
  +-- PASS ----------------------> log row, summary to you
  +-- REVISE, route: writer -----> back to Writer    (2 rounds maximum)
  +-- REVISE, route: researcher -> back to Researcher (2 rounds maximum)
  +-- still REVISE after 2 ------> delivered marked UNVERIFIED, open fixes listed
```

Your checkpoints:

1. Before the run, only if the question is ambiguous.
2. After the run: read the brief and the residual risks, then rate it 1-5 in `briefs/log.md`.
3. Before anything leaves the repo: the orchestrator asks before committing, pushing, publishing, sending, deleting, or spending.

## 5. Tool stack

| Need | Tool | Why |
|---|---|---|
| Agents and orchestration | Claude Code (this repo) | Runs the subagents and the `/brief` skill. No code to maintain. |
| Knowledge base | `briefs/` folder, markdown | Every brief keeps its evidence and review next to it. Searchable and versioned. |
| Database | `briefs/log.md` | One row per run. Enough to measure quality at this volume. |
| Version history | Git and GitHub | Records every change to prompts and outputs. |
| Task manager | None in version one | You are the only requester. Add one when a backlog exists. |
| Communication | The Claude Code session | Results arrive where you asked for them. |
| Automation | None in version one | Add a scheduled run only after the manual runs pass QA (see day 7). |

## 6. QA plan

**Checkpoints in every run**

- Researcher: every finding has a URL and a verbatim quote.
- Reviewer: claim-by-claim check, plus 3 source spot checks against the live page.
- You: read the Answer and the Residual risks.

**Failure modes and the control for each**

| Failure | Control |
|---|---|
| Invented or misquoted source | Verbatim quote required, and the Reviewer opens 3 URLs |
| Writer adds unsupported facts | Writer has no web tools, and every sentence needs an E-id |
| Reviewer approves everything | Fixed REVISE conditions, and you audit one PASS a week by hand |
| Stale information | Publication date recorded, and old sources flagged |
| Endless revision loop | Two rounds maximum, then delivery marked UNVERIFIED |
| Instructions hidden in a web page | Agents are told to treat page text as data |
| Scope creep | Scope note written first, and 600-word limit |

**Tests before relying on it**

1. A question you already know the answer to. The brief should match.
2. A question with no good answer. The brief should say so and not pad.
3. A question where sources disagree. The conflict should appear under "What is uncertain".
4. A manual audit: click every source in one brief and confirm each quote.

**Measuring whether it helps** (from `briefs/log.md`)

- Your rating averages 4 or higher.
- Your review takes under 10 minutes per brief.
- Zero wrong citations in the weekly manual audit.
- You use at least one brief a week for a real decision. If not, the use case is wrong and should change.

## 7. Seven-day launch plan

Total time is about 2 hours.

| Day | Time | Do |
|---|---|---|
| 1 | 15 min | Merge this branch. Open a new Claude Code session on the repo. Run test 1. |
| 2 | 15 min | Run tests 2 and 3. Note where the output was wrong. |
| 3 | 20 min | Run test 4, the manual audit. |
| 4 | 20 min | Adjust the agent files for the problems found. Change one thing at a time and rerun one test. |
| 5 | 15 min | Run your first real question. Rate it. |
| 6 | 15 min | Run two more real questions. Rate them. |
| 7 | 20 min | Review the log against the four measures. Decide: keep as is, adjust, or add a scheduled weekly brief. |

## START HERE FIRST

1. Merge the branch that contains this file into the default branch.
2. Start a new Claude Code session on this repo so the agents and the `/brief` skill load.
3. Type `/brief` followed by a question you already know the answer to.
4. Read `briefs/<slug>/brief.md` and `review.md`, then put a 1-5 rating in `briefs/log.md`.
