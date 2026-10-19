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
