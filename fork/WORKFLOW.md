# Git and GitHub workflow for the fork

Set up 2026-10-19. This is how we run two lanes in one repository: **our own version** and **fixes for
upstream**.

```
upstream/main (TecnaGamer) ──●────●────●────●──────────●─────►
                              \         \               \
up/36-no-map (PR to upstream)  ●──●──►PR \               \  (when the maintainer merges it, it
                                  \       \               \  comes back to us through the sync)
origin/main (ours) ──●──●─────────●M──●───●M(sync)───●────●M(sync)──►
                         \           /
my/<feature> (fork only)  ●──●──●───●
```

## Repository setup (already done 2026-10-19; redo it on a new machine)
```
git clone https://github.com/veilednet/BeamNG.Earth.git && cd BeamNG.Earth
git remote add upstream https://github.com/TecnaGamer/BeamNG.Earth.git
git remote set-url --push upstream NO_PUSH__open_a_PR_from_origin_instead
git fetch upstream
gh repo set-default veilednet/BeamNG.Earth      # bare `gh issue/pr` commands mean the fork;
                                                # upstream always gets an explicit -R TecnaGamer/BeamNG.Earth
```

## Picking a lane
Ask: **would TecnaGamer plausibly merge this?**

| Upstream fix (`up/<issue>-<slug>`)                     | Fork change (`my/<slug>`)                                   |
|--------------------------------------------------------|-------------------------------------------------------------|
| A bug in upstream code (crash, wrong output, a BeamNG compatibility break) | Our docs, tools, and agent setup (`CLAUDE.md`, `fork/`) |
| A fix for an upstream issue number                     | Opinionated changes in behavior, defaults, or UI            |
| A small, clearly better improvement to existing code   | Large features the maintainer hasn't asked for              |
| Something any player would want                        | Branding, our releases, our version scheme                  |

When unsure, use the upstream lane and keep the change small. A fix that upstream merges is one less
difference between their code and ours, which makes every future sync easier. A feature we'd also like upstream
to have can start in the fork, and later we send upstream a clean copy (cherry-picked, or rewritten as a branch
off `upstream/main`).

## Lane 1: fork changes
```
git switch main && git pull                      # our main, current
git switch -c my/<slug>
# ...work, commit...
<run the tests>
git switch main && git merge --no-ff my/<slug>   # "Merge my/<slug>: ..." (upstream merges the same way)
git push origin main
git branch -d my/<slug>
```
Small changes to docs or `fork/` can be committed straight to `main`. Add a line to "Fork-only changes" in
[STATUS.md](STATUS.md) for anything that changes behavior.

## Lane 2: upstream fixes
Each upstream fix lives in its **own worktree** under `.claude/worktrees/`. Upstream's `.gitignore` already
ignores `.claude/`. Because the worktree sits inside this checkout, a Claude session opened in it still loads
this repo's `CLAUDE.md` from the parent folder. Meanwhile the main checkout stays on `main` with our files
present.

```
git fetch upstream
# 1. Is it already handled?
git log upstream/main --oneline -20                       # fixed recently?
gh pr list -R TecnaGamer/BeamNG.Earth                     # someone else's open PR?
gh issue view <n> -R TecnaGamer/BeamNG.Earth --comments   # maintainer already replied or working on it?
# 2. Branch from upstream, not from our main
git worktree add .claude/worktrees/up-<n>-<slug> -b up/<n>-<slug> upstream/main
cd .claude/worktrees/up-<n>-<slug>
# ...fix, commit in upstream's style, run the tests...
git push -u origin up/<n>-<slug>                          # to OUR fork; fine without asking
# 3. The PR: only with the user's go
gh pr create -R TecnaGamer/BeamNG.Earth --base main --head veilednet:up/<n>-<slug> \
  --title "<one descriptive sentence>" --body-file <body.md>
# 4. We want the fix now too, without waiting for the maintainer
cd <repo root> && git switch main && git merge --no-ff up/<n>-<slug> && git push origin main
```
Record the PR in STATUS.md. When the PR is merged or closed, run `git worktree remove .claude/worktrees/up-...`
and delete the branch (`git branch -D`, plus `git push origin --delete up/...`).

**Review feedback on an open PR:** commit to the same `up/` branch and push to origin. The PR updates itself.
Pushing new commits to an open PR is public, so it needs the user's go. Merge the new commits into our `main`
as well.

**Testing an upstream fix in the game:** `install.py` copies the mod from whichever folder it's run in. Run
`python install.py --no-venv` from the worktree, test, then run it again from the repo root to put our version
back. To run the server, start it from the worktree using the main checkout's `.venv` Python.

### Upstream PR checklist
- [ ] One issue or one topic per PR. Small diffs get merged; mixed ones get stuck.
- [ ] Based on the latest `upstream/main` (if upstream moved, run `git rebase upstream/main` on the `up/`
      branch; never rebase `main`).
- [ ] Tests pass. New behavior has a test where upstream has tests for that area.
- [ ] No `VERSION` or `CHANGELOG.md` edits, and no `CLAUDE.md` or `fork/` files.
- [ ] The description says what was broken, the cause, the fix, how it was tested (unit and in-game, with the
      BeamNG version), `Fixes #<n>`, and a suggested changelog line. It ends with the Claude Code attribution
      line.
- [ ] The user approved opening it.

## Syncing with upstream (start of every session; upstream moves fast)
```
git fetch upstream
git switch main
git merge upstream/main          # a merge, not a rebase: main is public
<run the tests>
git push origin main
```
- **Conflicts with one of our `up/` fixes that upstream has since merged:** take upstream's version. Their
  merged copy is the reference.
- **Conflicts with a fork-only change:** keep our behavior but move it onto upstream's new code. Record it in
  STATUS.md if the conflict was hard. It's a sign the change should be sent upstream or reworked.
- `VERSION` and `CHANGELOG.md` only change on our side when we cut a fork release. Until then, upstream's
  versions win.

## Commits and pushing
- Message style: upstream's (see [CLAUDE.md](../CLAUDE.md)). End messages with the `Co-Authored-By` line the
  session gives you.
- Pushing to `origin` (our fork): branches anytime; `main` after the tests pass.
- Needs the user's go: anything that shows up on TecnaGamer's repo (PRs, comments, issues, pushes to an open
  PR), changing the fork's GitHub settings, and turning on Actions or publishing a release.

## Fork releases (off for now)
GitHub Actions has never been enabled on the fork, so `.github/workflows/release.yml` hasn't run there. Once it
is enabled, any push to `main` whose `VERSION` has no matching release builds and publishes `v<VERSION>`
publicly. Before turning it on, settle the fork's name and version scheme (see STATUS.md, Open decisions). Then
either change `VERSION` to the fork's scheme or add `if: github.repository == 'veilednet/BeamNG.Earth'` style
guards so we don't publish a release under upstream's version number.
