# Release gate: PR #2563 agent mouse_mode and bead updated_at

Evaluated: 2026-05-28T15:53:31Z

## Scope

- Deploy bead: `ga-6cc3qf` - Review: PR #2563 current head
- Source bead chain: `ga-in5wn7` -> `ga-688pwi` -> `ga-u9qua8`
- PR: https://github.com/gastownhall/gascity/pull/2563
- Branch: `builder/ga-xr5o0-1`
- Evaluated commit: `4d844d073`
- Current `origin/main`: `3203b502f`
- Merge base with `origin/main`: `f67cdc3e8`

The `docs/PROJECT_MANIFEST.md` file referenced by the deployer prompt is not
present in this checkout, so this gate uses the six release criteria from the
active deployer instructions.

## Criteria

| # | Criterion | Result | Evidence |
|---|-----------|--------|----------|
| 1 | Review PASS present | PASS | `ga-6cc3qf` notes contain `REVIEW VERDICT: PASS` for PR #2563 at `builder/ga-xr5o0-1 @ 4d844d073`. |
| 2 | Acceptance criteria met | PASS | `mouse_mode` is threaded through config, patch/override paths, pool copy, migration config, schemas, generated clients, template resolution, runtime startup hints, and runtime fingerprinting. The current head also carries bead `UpdatedAt` propagation through bead stores, query, API/genclient schema, and tests. |
| 3 | Tests pass | PASS | Focused tests, fast baseline, vet, dashboard check, dashboard smoke, schema freshness, and whitespace check all passed on the current head. |
| 4 | No high-severity review findings open | PASS | `ga-6cc3qf` review notes list no blocking or HIGH findings. Earlier findings on stale heads were non-blocking or addressed by rerouting/retry. |
| 5 | Final branch is clean | PASS | `git status --short` was empty before updating this gate file; this file is the only deployer change and is committed with the gate update. |
| 6 | Branch diverges cleanly from main | PASS | `git merge-tree --write-tree HEAD origin/main` exited 0 and produced tree `c35a78862ca7876b16b34353c10edba569ac46b0`; no merge conflict was reported against current `origin/main`. |

## Acceptance Evidence

- Default and empty `mouse_mode` preserve headless behavior by mapping to
  `MouseOn=false`.
- Explicit `mouse_mode = "off"` is accepted and preserves the default mouse-off
  startup behavior.
- Explicit `mouse_mode = "on"` maps to runtime `MouseOn=true` and skips tmux
  mouse/activity disable.
- Invalid `mouse_mode` values fail config validation.
- `AgentPatch`, `AgentOverride`, apply paths, migration config, schema
  generation, generated clients, and `deepCopyAgent` include the new field.
- Runtime fingerprint includes `MouseOn`, with `FingerprintVersion` bumped from
  `v2` to `v3`.
- Bead `UpdatedAt` is stamped and propagated through the current branch's bead
  store, API, genclient, and query surfaces.

## Validation

- `go test ./internal/config ./internal/runtime ./internal/runtime/tmux ./internal/migrate -count=1` - PASS
- `go test ./cmd/gc -run 'TestDeepCopyAgentCoversAllFields|Test.*Template|Test.*Pool' -count=1` - PASS
- `go test ./internal/beads ./internal/api ./internal/api/genclient -count=1` - PASS
- `go test ./test/docsync -run TestSchemaFreshness -count=1` - PASS
- `make test-fast-parallel` - PASS
- `go vet ./...` - PASS
- `make dashboard-check` - PASS
- `make dashboard-smoke` - PASS
- `git diff --check origin/main...HEAD` - PASS

## Push Target

Dry-run push to `origin` succeeded earlier in this deployer session. Use
`origin` for the final branch push.
