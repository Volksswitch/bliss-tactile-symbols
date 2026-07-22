# Working in this repo

## The provisioning bundle is never released from here

`Bliss Tactile Symbols.json` and `SVG files/` are **never** part of a release. They are
downloaded by users directly from the Volksswitch website, and from that point they are
the user's own files to maintain. Only the `.scad` auto-updates.

Practical consequences:

- **Never stage, commit, or push them.** A "bump bts" release covers the version-gated
  files only: `Bliss Tactile Symbols.scad`, `SCAD-CHANGELOG.md`,
  `latest_scad_version.json`. Do not let the bundle "ride along".
- **Never `git add` the `SVG files/` directory.** The repo lives in OneDrive and many of
  those SVGs are cloud-only placeholders; reading one forces a hydration that can wedge
  git in an uninterruptible kernel wait, holding `.git/index.lock` until the I/O resolves
  or the machine reboots. `git status` is safe (it only stats); reading content is not.

> Note: README.md and RELEASING.md still describe this repo as the canonical source for
> the bundle and say it ships with the next release. That is stale — this file is
> correct. Fix those two documents when convenient.

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
