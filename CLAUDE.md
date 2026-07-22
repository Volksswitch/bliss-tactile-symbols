# Working in this repo

## The provisioning bundle does not live here

`Bliss Tactile Symbols.json` (starter concepts) and `SVG files/` (starter Blissymbols) are
served to users as a ZIP from the Volksswitch website and maintained by Ken there. They are
**never** released from this repo. Copies of both were deleted from the project folder on
2026-07-22 and are `.gitignore`d.

- **A release covers the version-gated files only:** `Bliss Tactile Symbols.scad`,
  `SCAD-CHANGELOG.md`, `latest_scad_version.json`. Nothing else "rides along".
- **Do not reintroduce the bundle.** Nothing here needs it: the web app reads the JSON and
  SVGs from the folder the *user* connects, not from any repo, and the website ZIP is built
  from Ken's own copies. A copy kept here only drifts against the website's, and any fact
  derived from a drifted copy is wrong. If a sample of the `.json` format is needed (for
  tests, say), commit a small fixture of a few concepts instead.
- **Should `SVG files/` ever reappear, never `git add` it.** The repo lives in OneDrive and
  many of those SVGs are cloud-only placeholders; reading one forces a hydration that can
  wedge git in an uninterruptible kernel wait, holding `.git/index.lock` until the I/O
  resolves or the machine reboots. `git status` is safe (it only stats); reading is not.

Note: `svg_path` in the `.scad` resolves `graphic_svg` against a `SVG files/` folder. That
is the **user's** folder at render time, not this repo — leave those lines alone.

## If `.git/index.lock` is stuck

A git process wedged on OneDrive hydration cannot be killed and will hold the lock.
Rather than waiting, work through an alternate index, which never touches that lock:

```bash
export GIT_INDEX_FILE=.git/release-index
git read-tree HEAD
git add <files>
git commit -m "..."
```

Afterwards `cp .git/release-index .git/index && rm .git/release-index` to bring the real
index back in sync.

## Verifying `.scad` changes

Changes to the geometry should be rendered before they are called done, not just read:

```bash
"/c/Program Files/OpenSCAD/openscad.exe" -o out.stl -D 'add_hole_for_string="yes"' "Bliss Tactile Symbols.scad"
```

Check `Simple: yes` and that the volume count is unchanged from before the edit. The
`Can't open file '…/graphic.svg'` error is expected when `svg_path` is unset. Measuring
vertices out of the resulting STL is a good way to confirm a dimension is actually what
was intended.
