# renovate-config

The Renovate preset that sets the dependency-update policy for the operator's organisations. `default.json` is the
preset; the hub's `C:\dev\infra\claude-hub\dependency-updates.md` says what the policy does and how a repository
adopts it.

| Path | Holds |
|---|---|
| `default.json` | the preset every consumer extends |
| `renovate.json` | this repository's own config, which extends the preset, so Renovate keeps the pins below current |
| `package.json`, `package-lock.json` | the Renovate version the gate validates with, and the gate's command |
| `.nvmrc` | the Node version the gate runs on, locally and in `check.yml` |
| `.github/workflows/check.yml` | the gate on every pull request, on GitHub's runners |

## Who extends it

A repository extends the preset with `"extends": ["github>LunarianCode/renovate-config"]` in its `renovate.json`.
That reference names no tag or commit, so every consumer reads `default.json` from `main` at its next Renovate run, and
a merge here reaches all of them at once. `python C:/dev/infra/tools/ci/renovate_coverage.py` lists the repositories
whose config does not extend the preset and those with no Dependency Dashboard. The repository is public because a
GitHub App's token reaches only the repositories of its own owner.

## Changing the preset

1. Edit `default.json` in a worktree.
2. Run the gate before each commit, on the Node version in `.nvmrc`:

   ```
   npm ci
   npm run check
   ```

   It runs `renovate-config-validator --strict` over `default.json` and `renovate.json` with the pinned Renovate. Exit
   0 means both are valid and need no migration. Under `--strict`, a migration the validator asks for fails the gate,
   and the migration diff it prints is the change to make in `default.json`.
3. Open the pull request. `check.yml` runs the same gate, and the pull request merges on its green.
4. After the merge, a Renovate run has read the preset when a consumer's "Dependency Dashboard" issue shows an
   `updatedAt` later than the merge (`gh issue list --repo <owner>/<repo> --search 'author:app/renovate
   "Dependency Dashboard" in:title' --json number,updatedAt`), and `renovate_coverage.py` exits 0. This repository extends its own preset, so its own
   dashboard is the first consumer to read.
