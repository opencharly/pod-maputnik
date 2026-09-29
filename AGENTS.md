# AGENTS.md — pod-maputnik

Standalone candy repo for the `maputnik` candy — the Maputnik visual MapLibre GL
style editor, built from upstream source at image-build time and served as a
static SPA over HTTP. The entire candy lives in `charly.yml` at the repo root.

Canonical files:

- `charly.yml` — the `maputnik:` candy entity (description, `require`, `distro`,
  `port`, `service`, `plan`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-versa:maputnik-layer` — the owning skill: the Maputnik SPA build, the
  critical Vite `--base=/` override, and the asset-base lock-in check. Load
  before editing, building, deploying, or troubleshooting this candy.
- `/charly-versa:osm-tools-layer` — the martin vector-tile server the editor
  points at.
- `/charly-pod:pod` — the `kind: pod` / deploy schema reference (this candy is
  composed into a box; tree-position nesting, ports).
- `/charly-check:check` — the check/R10 framework: the `check:` step verbs and
  `charly check run <bed>`.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs, package sections, service declarations).
- `/charly-internals:git-workflow` — before any git/PR action.

The candy carries no `skill:` entity of its own; the family skill
`/charly-versa:maputnik-layer` covers the surface. The gap is routed to the named
skill-authoring batch
[opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291);
when that lands, add the owning skill here.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The candy's own `check:` steps are the R10 witness: they assert the built
  `dist` directory and `index.html`, the runnable node runtime, the running
  `maputnik` service, the HTTP 200 at the root on the published port, the absence
  of the `/maputnik/` asset prefix in the served HTML, and the reachable port.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.

## Modify this repo

- Edit the `maputnik:` candy entity in `charly.yml`.
- The build step MUST keep `npm run build -- --base=/`: Vite's default
  `--base=/maputnik/` bakes `/maputnik/assets/*` into the emitted HTML, which
  404s under the root-served `dist`.
- The `port:` field (`8000`), the `http.server` exec, and the served root
  (`/opt/maputnik/build`) must stay in step.
- `nodejs` / `npm` / `git` are the build inputs; a node-version break surfaces as
  a failed upstream `npm ci` / `vite build`.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
