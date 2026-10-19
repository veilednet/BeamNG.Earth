# Fork status board

The single place for what's going on. Update it when you start or finish anything, and give dates
(YYYY-MM-DD). Keep entries short and link details elsewhere.

**Fork base:** upstream `1.5.2-beta` (c703230), forked 2026-10-19.
**Last upstream sync:** 2026-10-19 (`main` == `upstream/main` + the fork setup commit).

## In progress
| Since | Lane | Branch / worktree | Task | Who |
|-------|------|-------------------|------|-----|
| (none) | | | | |

## Upstream PRs (ours)
| PR | Branch | Fixes | Opened | State | Notes |
|----|--------|-------|--------|-------|-------|
| (none yet) | | | | | |

## Fork-only changes
What our `main` has that upstream doesn't (besides `CLAUDE.md` and `fork/`).

| Date | Change | Commit / merge | Send upstream later? |
|------|--------|----------------|----------------------|
| 2026-10-19 | Fork setup: `CLAUDE.md`, `fork/` docs | (setup commit) | no |

## Long-lived fork branches (never merged into main)
| Since | Branch | What | Keeping it current |
|-------|--------|------|--------------------|
| 2026-10-19 | `my/psl` | The user's personal Windows-only Port St. Lucie, FL version (another session owns it) | merge `main` into it after each upstream sync |

## Test baseline (Windows, before any of our changes)
2026-10-19, at `44bfee1` (= upstream 1.5.2-beta + docs), Python 3.13 on Windows 11. Each server test file was
run on its own with a 5-minute limit (the whole suite in one run takes more than 15 minutes). **69 of 76 files
pass.** The failures were there before we changed anything; most look like upstream's tests assuming
Linux/POSIX. Each one is a possible small upstream PR ("tests: pass on Windows"):
- `test_autodrive_tool`: `tools/autodrive_perf.py` calls `os.sysconf`, which Windows doesn't have.
- `test_mapdata`: a cache path compared with `/` (Windows gives `\`), and `CREDITS.md` read without
  `encoding="utf-8"`, which garbles the `©`.
- `test_launcher_linux` (8 tests): Linux/Flatpak launcher tests that aren't skipped on Windows.
- `test_chunkcache` (`test_equal_to_full_build`) and `test_v131_default` (a different tile set than the golden
  file): not diagnosed yet. They may be Windows-specific or real.
- `test_server`: still running at 5 minutes (it may just be slow). `test_integration_drive` stops with an error
  after `[WinError 64] The specified network name is no longer available` and then hangs. Compare upstream
  issue #17 "Specified network name is unavailable".
- Mod Lua tests: not run yet (`luajit` isn't installed).

## Open decisions (the user decides)
- **Name and branding:** keep "BeamNG.Earth" or rename the fork? This affects the mod folder `beamng_earth`
  and the release file names.
- **Version scheme for fork releases:** for example `1.5.2-beta+vn.1` (upstream base + our counter). Fork
  releases stay off until this is decided (see WORKFLOW.md, Fork releases).
- **GitHub Issues on the fork:** currently disabled. Turn them on to track our own bugs on GitHub, or keep
  using this board.

## Upstream issues: triage
Snapshot 2026-10-19 (`gh issue list -R TecnaGamer/BeamNG.Earth`). Upstream also has two open PRs from
hashk014: #34 (DuckDB source builds in the Windows installer) and #35 (traffic on roundabouts and narrow
streets). Don't duplicate them.

Likely good first upstream fixes: specific, reproducible, and smaller in scope.
- #1: real-time sky changes the time of day on other levels (sky.lua onUpdate has no level check). The reporter
  named the cause.
- #36 / #37 / #32 / #12: no map / map doesn't generate or open / world empty. Possibly one root cause, and
  possibly 0.40-related. Check the 1.5.2 commits first.
- #38: waypoints lead nowhere. #21: autodrive brakes and turns around.
- #8: Netherlands below-sea-level terrain flooded by the sea plane.
- #15 / #2: buildings and houses floating above the ground or road.
- #23 / #7 / #17: world server can't connect (sourceforge download, network name unavailable).
- #11: duckdb wheel build fails (check that hashk014's #34 doesn't already cover it).

Bigger or design-level (more likely fork lane, or discuss on the issue first): #24 / #29 sidewalks, #25
bridges, #31 road materials, #33 / #30 / #28 / #10 preload and load priority, #39 / #40 performance and
generation quality.

Enhancement ideas (fork lane unless the maintainer agrees): #3 street lights, #4 multiplayer, #5 desert rocks,
#9 C/C++ port, #14 doors, #16 trains, #18 street view, #20 gas stations.

Not code: #13 / #22 "is this mod malicious" (antivirus false positives, 15 comments), #26 a how-to question.

## Log
- 2026-10-19: Forked. Remotes: `origin` = veilednet, `upstream` = TecnaGamer (push blocked). `gh` default repo
  = the fork. Wrote `CLAUDE.md` and the `fork/` docs. Fork Actions never enabled, so no releases.
