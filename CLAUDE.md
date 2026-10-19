# BeamNG.Earth: veilednet's fork

This checkout is **veilednet's fork** of TecnaGamer's BeamNG.Earth (MIT). We do two kinds of work here:

1. **Our own version** of the project: changes we want, on our own schedule, on `origin/main`.
2. **Fixes we send back** to the original project as pull requests.

This file and everything under [fork/](fork/) belong only to the fork. They never go into a pull request to
upstream.

| Remote     | Repo                                                                         | We can                  |
|------------|------------------------------------------------------------------------------|-------------------------|
| `origin`   | [veilednet/BeamNG.Earth](https://github.com/veilednet/BeamNG.Earth)          | push                    |
| `upstream` | [TecnaGamer/BeamNG.Earth](https://github.com/TecnaGamer/BeamNG.Earth)        | fetch, open PRs to it   |

Pushing to `upstream` is blocked on purpose: its push URL is set to `NO_PUSH__open_a_PR_from_origin_instead`.

## Read before working
- [fork/WORKFLOW.md](fork/WORKFLOW.md): **the git and GitHub rules.** Which lane a change goes in, the exact
  commands for each lane, syncing with upstream, and the PR checklist.
- [fork/STATUS.md](fork/STATUS.md): **the task board.** What's in progress, PRs we've sent upstream,
  fork-only changes, open decisions, and upstream issues we're tracking. Update it whenever you start or finish
  something.
- [fork/HANDOFF.md](fork/HANDOFF.md): how to hand a task to another agent (the brief template, the report
  format, and what agents must not do).
- The project's own docs: [README.md](README.md), [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md),
  [docs/ROADMAP.md](docs/ROADMAP.md), and [CHANGELOG.md](CHANGELOG.md).

## Hard rules
1. **Pick the lane first.** A fix that upstream would plausibly accept is an **upstream fix**: branch `up/...`
   from `upstream/main`, in its own worktree. Everything else is a **fork change**: branch `my/...` from `main`.
   Never branch an upstream fix from our `main`, because the PR would carry all our fork-only commits with it.
2. **Get the user's go each time before doing anything public on upstream:** opening a PR, commenting on an
   issue or PR, or pushing more commits to a PR that's already open.
3. **Never** force-push or rebase `main`. Never commit secrets, caches, `.venv/`, `dist/`, or test-run folders.
4. **Fork releases are off.** GitHub Actions on the fork has never been enabled, so `release.yml` hasn't run
   there. Once it's on, every push to `main` with a new `VERSION` publishes a public release. Leave it off until
   the user decides the fork's name and version scheme (see Open decisions in STATUS.md).
5. **Upstream moves fast** (hundreds of commits, many issues a day). Run `git fetch upstream` before starting
   anything. Before fixing an upstream issue, check it isn't already fixed in `upstream/main` and that no open
   upstream PR already covers it.
6. Upstream PRs don't touch `VERSION` or `CHANGELOG.md`. The maintainer handles releases. Put a suggested
   changelog line in the PR description instead.
7. Run the tests before every merge and every PR:
   - server: `cd server && python -m unittest discover -s tests -p "test_*.py"`
   - mod: `cd mod/tests && for f in test_*.lua; do luajit $f; done`
   - Some changes can only be checked in the game. Say what was tested in-game and what wasn't.

## How upstream writes code
Match it. Commit subjects are one descriptive sentence saying what changed and why, often with a `scope:`
prefix (`tools/await_update.py: ...`, `BeamNG 0.40 ...`). Comments are dense and explain why. The docs record
decisions with dates. Python is standard library first, and the mod is Lua. Read the surrounding code before
writing any.
