# pod-maputnik

The `maputnik` candy of the OpenCharly candy library, as a standalone repo
(kind-prefixed naming). It ships the Maputnik visual MapLibre GL style editor,
built from upstream source and served as a static SPA.

## What it provides

Clones the upstream `maplibre/maputnik` repo at image-build time, runs
`npm ci` + `npm run build -- --base=/`, and copies Vite's emitted `dist/` to
`/opt/maputnik/build/`. The `--base=/` override keeps asset URLs root-relative so
they resolve under the serve path instead of 404ing at `/maputnik/assets/*`. The
stdlib `python3 -m http.server 8000 --directory /opt/maputnik/build` runs as the
always-restart `maputnik` service.

| Property | Value |
|---|---|
| Service | `maputnik` (`/usr/bin/python3 -m http.server 8000 --directory /opt/maputnik/build`, priority 34) |
| Port | `8000` (static SPA HTTP) |
| Requires | `layer-supervisord` |
| Packages | `nodejs`, `npm`, `git` (Arch and Fedora) |
| Build artifact | `/opt/maputnik/build/` (Vite `dist/`) |

It pairs with the `osm-tools` martin tile server — the editor at `:8000` points
at the tile catalog at `:3000`. Every fact is observable: the built SPA `dist` +
`index.html`, the node build toolchain, the running service, the HTTP 200 the
editor returns, and the absence of the `/maputnik/` asset prefix in the served
HTML.

## How to use it

```bash
charly box build maputnik
charly config maputnik
charly start maputnik
# editor: http://localhost:8000
```

The candy's own `check:` steps assert the built `dist` directory and
`index.html`, the runnable node runtime, the running `maputnik` service, the
HTTP 200 at the root, the absence of the `/maputnik/` asset prefix in the served
HTML, and the reachable port.

## Layout

- `charly.yml` — the `maputnik:` candy entity (description, `require`, `distro`,
  `port`, `service`, `plan`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-versa:maputnik-layer` — the Maputnik SPA build (the
  Vite `--base=/` override, the asset-base lock-in check).
- `/charly-versa:osm-tools-layer` — the martin vector-tile server the editor
  points at.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
