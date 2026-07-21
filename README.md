# Bliss Tactile Symbols — symbol designer `.scad`

The canonical OpenSCAD program that turns a prepped Blissymbol SVG into a finished,
3D-printable tactile symbol. This is the **combined designer + graphic maker**
(`Bliss Tactile Symbols.scad`) used by the
[Bliss Tactile Symbol Designer web app](https://github.com/Volksswitch/bliss-tactile-symbols-web).

**Author:** Volksswitch (www.volksswitch.org) — released to the public domain (CC0).

## Why this repo is separate from the web app

The `.scad` and the web app version **independently**. This repo hosts the published
`.scad` and a small manifest so the web app can offer users an in-app update of their
local copy without a web-app release — and a web-app release never forces a `.scad`
change. See [RELEASING.md](RELEASING.md).

## Contents

- `Bliss Tactile Symbols.scad` — the program. Its `scad_version = N;` (in the
  `/*[Hidden]*/` block) is the version users are compared against.
- `latest_scad_version.json` — the update manifest the web app fetches
  (`version`, `scad_filename`, `scad_url`, `notes`).
- `SCAD-CHANGELOG.md` — user-facing changes; its bullets show verbatim in the
  web app's update dialog.
- `scripts/publish-scad-version.mjs` — regenerates the manifest from the `.scad`
  version + changelog.

## Standalone use

The `.scad` also runs directly in desktop OpenSCAD: set `svg_path` to a real prepped
SVG and render. The web app writes the chosen SVG into the WASM filesystem and drives
the same parameters.
