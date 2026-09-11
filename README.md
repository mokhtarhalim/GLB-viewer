# GLB Viewer

**Live demo: https://mokhtarhalim.github.io/GLB-viewer/**

A single-page browser tool for opening `.glb`/`.gltf` 3D model files, reading
their stats (triangle count, materials, textures, compression extensions
used), and comparing two models side by side with synced cameras.

No backend, no build step, no npm dependencies at runtime — it's one HTML
page plus two vendored, self-hosted libraries. Everything runs client-side;
a dropped file is read locally in the browser (`URL.createObjectURL`) and
never uploaded anywhere.

## Features

- Open one `.glb`/`.gltf` via the file picker, or drag-and-drop anywhere on
  the page.
- **Compare A/B**: drop or pick two files at once (or drag a second file
  directly onto the right-hand viewport) to view them side by side with
  synced camera orbit/target/FOV — drag either one, the other follows.
- Right-hand panel: file size, load time, triangle count, mesh/primitive/
  material/node counts, per-texture dimensions and byte size, texture share
  of total file size, glTF `extensionsUsed` (flags Draco / Meshopt / WebP /
  KTX2 specifically), and the exporter's `asset.generator` string. When
  comparing, also shows the size/triangle/texture delta between A and B.
- Auto-rotate, dark/light stage background, reset camera, hide/show the
  details panel.

## Run it locally

Because the page loads an ES module (`<script type="module">`) and fetches
a `.wasm` file, opening `index.html` directly via `file://` will fail in
most browsers (module/fetch requests are blocked from `file://` origins).
Serve it over local HTTP instead:

```bash
npm run dev
```

This runs `npx serve` on `http://localhost:5173` with no install step
(`serve` is fetched on demand via `npx`). Any other static file server
works identically — e.g. `python -m http.server 5173`.

## Deploy it

This is a fully static site — no server code, no environment variables, no
build command. Any static host works:

- **GitHub Pages**: enable Pages on the repo, serve from the root of the
  default branch. All internal links are relative (`./glb-viewer/…`,
  `./draco/…`), so it works from a project subpath
  (`username.github.io/repo-name/`) as well as a root domain.
- **Vercel**: import the repo, framework preset "Other", no build command,
  output directory `.` (the repo root).
- **Netlify**: same — no build command, publish directory `.`.
- **Cloudflare Pages**: no build command, output directory `/`.

## What's in here

```
index.html              the entire app: markup, styles, and all JS logic
glb-viewer/mv-module.js  vendored ESM build of <model-viewer> (Google, BSD-3-Clause)
draco/                   vendored Draco decoder (wasm + JS glue) used by <model-viewer>
                         for KHR_draco_mesh_compression geometry
robots.txt, sitemap.xml  basic SEO plumbing for the hosted site
```

`mv-module.js` and the `draco/` decoder are pinned, self-hosted third-party
builds rather than pulled from a CDN at runtime — this keeps the page
working fully offline once loaded, and immune to any CDN outage or version
drift.

## Credits

- [`<model-viewer>`](https://modelviewer.dev/) by Google — BSD-3-Clause.
- [Draco](https://github.com/google/draco) geometry compression — Apache-2.0.
