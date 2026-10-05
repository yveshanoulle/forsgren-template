# forsgren installation

This repository publishes a [forsgren](https://github.com/yveshanoulle/forsgren)
page for your projects, once a day, on GitHub Pages. forsgren measures the four
DORA metrics from data GitHub already has.

Today (forsgren 0.0.3) the page shows **deployment frequency** per project:
successful production deployments in the last 7 days, the latest one, and the
DORA band with its 30-day count. The other three metrics follow in later
releases.

This template holds only forsgren's own files. Your configuration and your
history are yours: they are never in the template, so installing a new
version never overwrites them.

## Set up

1. **Use this template** (the green button) to create your own repository.
   Private or public, your choice.
2. **Create a read-only token** for the repositories you want to measure:
   GitHub → Settings → Developer settings → Fine-grained tokens. Give it
   read-only **Deployments** (for `environment=`, the default), **Actions**
   (for `workflow=`), **Contents** (for `release`) and **Issues** (failures,
   later); Metadata is added by itself. Store it in your new repository under
   **Settings → Secrets and variables → Actions → New repository secret**,
   named `FORSGREN_TOKEN`.
3. **Settings → Pages → Source: GitHub Actions**.
4. **Actions → forsgren → Run workflow** once. On a new install the run
   commits a starter `forsgren.config.yml` and publishes a page saying no
   projects are configured yet.
5. **Edit `forsgren.config.yml`** and list your projects and repositories, for
   example:

   ```yaml
   version: 1
   projects:
     - name: Acme Shop
       repositories:
         - name: acme/api                        # default: GitHub Deployments
                                                 # to the environment production
         - name: acme/ios-app
           deployment: workflow=testflight.yml   # a successful run of that workflow
     - name: Acme Tools
       repositories:
         - name: acme/cli
           deployment: release                   # a published GitHub Release
   ```

   `environment=<name>` is the form for another environment. The daily run
   refuses an invalid file with a one-line message.

From then on it runs every day by itself: it collects the new deployments,
commits them to `data/` and publishes the page. If your default branch
refuses pushes from `github-actions[bot]`, allow it to bypass that rule, or
the run cannot commit.

## What is in here

- `.github/workflows/forsgren.yml`: the daily run. It calls forsgren's
  reusable workflow at a pinned release.
- `.github/workflows/forsgren-update.yml`: merges Dependabot's forsgren pull
  request for you when `forsgren.config.yml` says `auto_update: true`.
- `.github/dependabot.yml`: proposes each new forsgren release as a pull
  request, every day at 04:00 UTC.
- `README.md` and `LICENSE`: this text and its licence.

What you add or the run writes, and what forsgren never overwrites:

- `forsgren.config.yml`: your configuration. The first run writes a starter
  one if you have none; an existing file is never touched.
- `data/`: your history (`data/deployments.csv`), written and committed by the
  daily run as `github-actions[bot]`. forsgren only ever appends to it.

## Installing a new version

**One click, or none.** When forsgren publishes a release, Dependabot opens a
pull request here that moves the pinned `uses:` lines in
`.github/workflows/forsgren.yml` and `.github/workflows/forsgren-update.yml` to
the new release (configured in `.github/dependabot.yml`). It never touches
`forsgren.config.yml` or `data/`.

- With `auto_update: true` in `forsgren.config.yml`, the pull request merges
  itself and forsgren runs right after. `auto_update_level` sets how far an
  update may go: `patch` (0.2.0 to 0.2.1), `minor` (to 0.3.0) or `major` (to
  1.0.0). The starter config has `auto_update: true` and
  `auto_update_level: patch`.
- Anything else is left for you, with the reason in the run's summary. That
  covers a bigger update than your level, a pull request that changes more
  than the pin lines, or one that is not Dependabot's.
- With `auto_update: false`, or no `auto_update` line, merge the pull request
  yourself; the next run uses the new version.

**When a release changes more than that line** (a permission, a new secret),
its release notes say so. Then copy the files of the new forsgren-template over
your repository once: that replaces forsgren's files (the workflows, this
README) and leaves `forsgren.config.yml` and `data/` as they are. Your
repository keeps its secrets and its Pages address.

Coming from v0.1.3 or earlier: copy `.github/workflows/forsgren-update.yml`
once, and add `auto_update: true` and `auto_update_level: patch` to
`forsgren.config.yml` if you want updates to merge themselves (forsgren#58).

Coming from v0.0.1 or v0.0.2: copy the files once (v0.0.3 asks for
`contents: write` and passes `FORSGREN_TOKEN`), and add that secret (Set up,
step 2) before the next run.

## Licence

The files in this template are under the [0BSD licence](LICENSE): use them
as you like, no attribution needed. forsgren itself is EUPL-1.2.
