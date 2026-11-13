# renovate-config

The shared Renovate preset. `README.md` gives the files, the gate and how a change is proved; the hub's
`C:\dev\infra\claude-hub\dependency-updates.md` owns the update policy and how a repository adopts it.

- Gate before each commit: `npm ci`, then `npm run check`, on the Node version in `.nvmrc`. `check.yml` runs the same
  gate on every pull request.
- Consumers read `default.json` from `main` with no pin, so a merge changes the update policy of every consumer at
  its next Renovate run. Do not merge a preset change before `check.yml` passes on it, or an invalid preset stops
  Renovate in every consumer.
- A change to what the policy does goes into `dependency-updates.md` in the same pass, through Claude Hub - Main.
- The Node version in `.nvmrc` satisfies the `engines.node` range of the Renovate version `package.json` pins
  (`npm view renovate@<version> engines`). Bring both up together.
