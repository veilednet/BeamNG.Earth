# Handing work to agents

The **lead** is the main Claude session talking with the user. It owns the plan, [STATUS.md](STATUS.md), and
everything public. **Agents** each do one bounded task and report back. Agents start with no memory of the
conversation, so the brief is all they know.

## Rules for every agent
- Work only in the branch and folder named in the brief. For an upstream fix, that's its worktree under
  `.claude/worktrees/`. Don't switch branches in the main checkout.
- Commit locally on your branch. **Don't** push, open PRs, comment on GitHub, merge into `main`, or change
  repo settings. The lead does those after reviewing your work.
- Upstream-lane work must not touch `VERSION`, `CHANGELOG.md`, `CLAUDE.md`, or `fork/`.
- Run the tests (see [CLAUDE.md](../CLAUDE.md)) and report the real output: passes, failures, and anything you
  couldn't run.
- If you find the task is wrong or much bigger than briefed (already fixed upstream, needs a design decision,
  touches many systems), stop and report instead of widening the scope.
- Match upstream's code and commit style.

## Brief template (the lead fills this in)
```
Task: <one sentence>
Lane: upstream fix | fork change
Issue: TecnaGamer/BeamNG.Earth#<n> (<url>), or none
Branch: up/<n>-<slug> | my/<slug>
Folder: <absolute path of the worktree, or the repo root>   (the lead created it from <base>)
Context: <what we already know: the suspected cause, files, related commits or docs, what the reporter said>
Done when: <the specific result: test X passes, behavior Y in the game, a doc section written>
Out of scope: <what not to touch>
Read first: CLAUDE.md, fork/WORKFLOW.md, <relevant docs/ARCHITECTURE.md sections>
Report back in the format in fork/HANDOFF.md.
```

## Report format (the agent returns this)
```
Result: done | partial | blocked | not needed (already fixed upstream in <commit>)
Branch / commits: <branch>, <short hashes + subjects>
What changed: <files and why, a few lines>
Root cause: <for bugs>
Tests: <command + result; in-game: what was checked, BeamNG version, or "not tested in game">
Risks / follow-ups: <anything the lead should know>
Suggested PR title + changelog line: <for upstream-lane work>
```

## After the report (the lead)
1. Review the diff (`git -C <worktree> log -p upstream/main..HEAD`) and rerun the tests.
2. Update STATUS.md.
3. Fork lane: merge into `main` and push. Upstream lane: push the branch to origin, and **ask the user** before
   opening the PR. Then merge the branch into our `main`.

## Running agents in parallel
Give each task its own branch and worktree. Never let two agents edit the same files. Prefer upstream-lane
tasks that don't overlap (for example the sky level check in #1 and the sea plane in #8), because each one
becomes its own PR.
