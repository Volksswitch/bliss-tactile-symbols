# Releasing the symbol designer `.scad`

The formal release process for the Bliss Tactile Symbols `.scad`. Same **shape** as the
Keyguard Designer `.scad` (*work locally, log each user-visible change to `## Unreleased`
in plain language, and say "bump bts" to cut a release*) and the BTS web app. Released
**independently** of the web app: pushing here never redeploys the app, and an app
release never touches the `.scad`.

## Environment model

- **The PC is the development environment.** All work is committed to the local `main`
  branch and synced across machines by OneDrive (including `.git`). **These commits are
  NOT pushed.**
- **GitHub is the release environment.** There is no served app here — the BTS web app's
  in-app updater downloads `main/Bliss Tactile Symbols.scad`, and the Volksswitch website
  serves the provisioning ZIP. So the copy of the `.scad` on `main` **is** what users get.

  > **Commit to local `main` = save your work.
  > Push `main` = release to users.**

  Everything piles up locally, invisible to users, until you release. **We do not push
  between releases.** One branch: `main`.

## This repo holds the `.scad` and nothing else that ships

The `.scad` is authored **here**, and this repo releases **only** it:
`Bliss Tactile Symbols.scad` + `latest_scad_version.json` + `SCAD-CHANGELOG.md` — the
version-gated trio that drives the in-app update. Ken's
`…/Desktop/Bliss Tactile Symbols/` folder is a **downstream** scratch/test area — he
copies the `.scad` *out* to it to render/test STLs; nothing syncs back. **Claude ignores
that working folder.**

**The provisioning bundle is not here.** `Bliss Tactile Symbols.json` (the starter
concepts) and `SVG files/` (the starter Blissymbols) are served to users as a ZIP from
the Volksswitch website and are maintained by Ken there; once provisioned they are the
user's own files. They are **never** released from this repo, are `.gitignore`d, and
copies of them were deleted from the project folder (2026-07-22) so nothing here can go
stale against the website's. Do not reintroduce them: a copy that drifts is worse than
no copy, and reading `SVG files/` out of OneDrive is what wedged git that morning. If a
sample of the `.json` format is ever needed (e.g. for tests), commit a small fixture of
a few concepts rather than the full file.

## Version numbers

Two things carry the version:

- **`scad_version`** (integer, in the `.scad`'s `/*[Hidden]*/` block) — the version banner
  and the number the web app compares against.
- **`latest_scad_version.json`** — the manifest the app's updater compares against; its
  `version` + `notes` are regenerated from the constant + `SCAD-CHANGELOG.md` by
  `scripts/publish-scad-version.mjs`.

**Pre-bump (so you always know which build you're testing).** At the *end* of each
release, the local dev copy is immediately pre-incremented: `scad_version` → (last public
release + 1). A `.scad` you copy out to test then always reads a number **higher than the
last public release**. This pre-bump lives **locally only (unpushed)** until its release.

**The updater gate — why the pre-bump must be held off `main`.** The app's updater
downloads `main/Bliss Tactile Symbols.scad`, reads its `scad_version`, and **aborts** if
it does not equal the manifest's `version`. So on GitHub `main`, the `.scad` and
`latest_scad_version.json` must **both equal the currently-released version and each
other**. Never push a pre-bumped `.scad` ahead of a matching manifest. The number only
ever increases.

## Releasing — trigger phrase "bump bts"

Ken says **"bump bts"** (distinct from **"bump bts web app"**, which releases the web
app). He issues it only **after verifying `SCAD-CHANGELOG.md`.** That single command
authorizes the whole ritual **through the push** — Claude runs it end to end and does
**not** pause for a second confirmation. Let `N` = the pre-bumped `scad_version`.

1. **Verify** `scad_version` reads `N` (it was pre-bumped at the last release).
2. **Finalize the changelog.** Rename the topmost **`## Unreleased (next version)`**
   heading to **`## Version N`**, and add a fresh empty `## Unreleased (next version)`
   above it.
3. **Regenerate the manifest:** `node scripts/publish-scad-version.mjs` — writes
   `version: N` and the `## Version N` bullets into `latest_scad_version.json`. It errors
   if the notes are still a placeholder. Confirm the number matches.
4. **Commit** the release (`Bliss Tactile Symbols.scad`, `SCAD-CHANGELOG.md`,
   `latest_scad_version.json`) as one commit, so `main`'s `.scad` and manifest both say
   `N` at the same moment.
5. **Push `origin main`.** The updater now offers `N` to users on older versions.
6. **Rebuild the website ZIP** — Ken's, from his own copies of the `.json` and
   `SVG files/`, dropping in the `.scad` at `N` from `main`, so new users start on the
   released version. Not a step Claude can run; the bundle files do not live in this repo.
7. **Start the next cycle — pre-bump.** Increment `scad_version` to `N+1`, commit that
   locally, and **do not push.**

## Invariants — do not break these

- **Never push to `main` except as step 5 of "bump bts".** The `.scad` on `main` is what
  users download. Between releases, everything stays as local commits.
- **On `main`, the `.scad`'s `scad_version` and the manifest's `version` must always
  match** (the updater aborts otherwise). The pre-bumped `.scad` stays local until its
  manifest is pushed with it.
- **The version only ever increases** (never reused, never lowered).
- **`SCAD-CHANGELOG.md` is authored as-you-go**, in user language; nothing is authored at
  release except the `## Unreleased` → `## Version N` rename.
- **Keep `scad_url` in the manifest and `SCAD_REPO` in the web app's `app.html` pointing
  at this repo.**

## Rolling back a bad release

Revert the release commit on `main`, bump `scad_version` **up** again (e.g. `4` → `5`,
never back to `3`), regenerate the manifest so it matches, and push both together — same
"`.scad` and manifest agree on `main`" rule as a forward release.
