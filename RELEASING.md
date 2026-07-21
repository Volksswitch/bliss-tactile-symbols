# Releasing the symbol designer `.scad`

This repo is released **independently** of the web app
(`Volksswitch/bliss-tactile-symbols-web`). Pushing here never redeploys the app, and
an app release never touches the `.scad`. The web app fetches this repo's
`latest_scad_version.json` and, when a user's local `.scad` is behind, offers an
in-place update.

## Environment model

- **The PC is the development environment.** Work is committed to local `main` and
  synced by OneDrive. Commits are **not** pushed until you publish.
- **GitHub is the publish target.** `main` on
  <https://github.com/Volksswitch/bliss-tactile-symbols> is what the web app reads
  (via `raw.githubusercontent.com` — no GitHub Pages needed here).

  > **Commit to local `main` = save. Push `main` = publish a `.scad` update to users.**

## Where the working `.scad` lives

The `.scad` is developed in Ken's working folder
(`…/Desktop/Bliss Tactile Symbols/`, alongside the presets `.json`, `SVG files/`, and
generated STLs). The copy in **this** repo is the **published** copy. When publishing,
copy the working `.scad` here (overwriting).

## Version numbers

`scad_version = N;` in `Bliss Tactile Symbols.scad` (in the `/*[Hidden]*/` block) is the
only version marker. It only ever **increases**. The web app compares the user's local
`scad_version` against `latest_scad_version.json`'s `version` (they must match the
`.scad` this repo publishes).

## Publishing — trigger phrase "publish scad version"

1. **Bump `scad_version`** in the working `.scad`, and **copy it here** as
   `Bliss Tactile Symbols.scad` (overwriting).
2. **Log the change** in `SCAD-CHANGELOG.md`: rename the top `## Unreleased (next version)`
   heading to `## Version N` (matching `scad_version`), and add a fresh empty
   `## Unreleased` above it. Bullets are user language — they show verbatim in the
   web app's update dialog.
3. **Generate the manifest:** `node scripts/publish-scad-version.mjs` (errors if the
   notes are still a placeholder — never advertise a version with no written notes).
4. **Commit** `Bliss Tactile Symbols.scad`, `latest_scad_version.json`, and
   `SCAD-CHANGELOG.md` together.
5. **Push `origin main`.** Users are offered the update the next time they open their
   folder in the web app.

## Invariants

- **`scad_version` and the manifest `version` only ever increase and must agree** with
  the `.scad` on `main`. The web app re-reads the version from the downloaded bytes and
  refuses a mismatch, so a mispointed manifest fails safe (no change to the user's file).
- **`SCAD-CHANGELOG.md` is authored as-you-go**, in user language; the only release-time
  edit is the `## Unreleased` → `## Version N` rename.
- **This repo is independent of the web app.** Keep `scad_url` in the manifest pointing
  at this repo, and keep `SCAD_REPO` in the web app's `app.html` matching it.
