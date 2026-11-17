---
name: brief
description: Run the research team (researcher, writer, reviewer) on a question and deliver a cited, reviewed brief. Use when the user types /brief or asks for a research brief.
---

Run the research-brief pipeline for this question: $ARGUMENTS

You are the orchestrator. You pass work between agents and report to the user. You do not research, write, or review yourself.

## Steps
1. **Scope.** If the question is clear, write a 2-line scope note (what is in, what is out, time period) and continue. Ask the user only if the question could mean materially different things. Pick a short kebab-case slug and create `briefs/<slug>/`.
2. **Research.** Run the `researcher` agent with the question, scope note, and folder path.
   - If it escalates or returns fewer than 3 findings, stop and report to the user.
3. **Write.** Run the `writer` agent with the question, scope note, and folder path.
4. **Review.** Run the `reviewer` agent with the question, scope note, and folder path.
5. **Loop.** If the verdict is REVISE, send the fixes to the agent named in Route, then rerun the later steps (researcher, then writer, then reviewer; or writer, then reviewer). Two revision rounds at most.
6. **Log.** Append one row to `briefs/log.md`:
   `| date | slug | findings | revision rounds | final verdict | user rating (blank) |`
7. **Report.** Give the user the Answer section of the brief, the verdict, the residual risks, and the path to `brief.md`.
   - If the verdict is still REVISE after two rounds, deliver the brief marked UNVERIFIED and list the open fixes. Do not present it as reviewed.

## Approval gates (stop and ask the user first)
- Committing or pushing anything to git.
- Sending, posting, or publishing a brief anywhere outside this repo.
- Deleting or overwriting an existing `briefs/<slug>/` folder.
- Anything that costs money or needs a login. Never ask for passwords or keys in chat.
