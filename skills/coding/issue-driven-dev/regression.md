# Regression gate — discovering commands

Run these checks on the **feature branch** before Phase 4. Record the chosen commands in the session so re-runs stay consistent.

## Decision tree

1. **CI workflows** — Read `.github/workflows/*` (or GitLab CI, Circle, etc.) for the job that runs on pull requests. Note the exact commands in `run:` steps.
2. **Package scripts** — If the repo root has `package.json`, check `scripts` for `test`, `build`, `lint`, `typecheck`, `check`.
3. **Task runners** — `Makefile`, `justfile`, `Taskfile.yml`, `turbo.json`, `nx.json` — prefer documented targets used in CI.
4. **Language defaults** (only if CI does not spell them out):
   - Rust: `cargo test`, `cargo build`
   - Go: `go test ./...`, `go build ./...`
   - Python: project’s documented pytest/tox/uv/poetry command
   - JVM: `./gradlew test build` or `./mvnw verify`

## Minimal set rule

Include every step CI would fail on for a typical code change:

- **Tests** — always
- **Build** — always if CI builds artifacts or runs `tsc`/compile
- **Lint / format / typecheck** — only if CI runs them on PRs

Skip optional jobs (deploy, e2e on schedule) unless the issue touches that surface or the user asks.

## When ambiguous

Ask once:

> Which commands should I treat as the regression gate for this repo? (I see CI running X and Y; I can also run Z from package.json.)

Then run exactly that set until green.

## Session template

After discovery, note internally:

```
Regression gate:
- test: …
- build: …
- lint (if applicable): …
```

Re-run the full gate after each fix cycle during Phase 3.
