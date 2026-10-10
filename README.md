# forsgren installation

This repository publishes a [forsgren](https://github.com/yveshanoulle/forsgren)
page for your projects, once a day, on GitHub Pages. forsgren measures the five
DORA metrics from data GitHub already has: deployment frequency, lead time for
changes, failed deployment recovery time, change fail rate and deployment rework
rate, per project and per service.

The page has three views: **standard** (the DORA bands), **numbers** (the raw
values) and **scoring** (each metric's DORA Quick Check score, 0 to 10, and
Overall Performance). `view:` in your config chooses which one is the root page. The settings page
lists every setting with the value in use, marking the ones you haven't set.
The issues page, linked next to the views, shows per day how many issues were
opened and completed across all your projects, yesterday first.
The full documentation is in the
[forsgren README](https://github.com/yveshanoulle/forsgren#readme).

This template holds only forsgren's own files. Your configuration and your
history are yours: they are never in the template, so installing a new
version never overwrites them.

## Set up

1. **Use this template** (the green button) to create your own repository.
   Private or public, your choice.
2. **Create a read-only token** for the repositories you want to measure:
   GitHub → Settings → Developer settings → Fine-grained tokens. Give it
   read-only **Deployments** (for `environment=`, the default), **Actions**
   (for `workflow=`), **Contents** (for `release`) and **Issues** (for
   `failure` issues); Metadata is added by itself. Store it in your new repository under
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
   view: standard              # standard, numbers or scoring
   auto_update: true           # Dependabot's forsgren pull requests merge themselves
   auto_update_level: patch    # possible options: major, minor, patch
   history_days: 365           # how far back history goes, 1 to 1825 days
   history_chunk_days: 100     # how many days back each run adds, 1 to 365
   working_hours: 8            # a working day is configured as this many hours, 1 to 24
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

The forsgren job in `.github/workflows/forsgren.yml` grants itself only what it
needs, each permission declared explicitly:

- `contents: write`: commit a new install's starter `forsgren.config.yml` and
  the collected history in `data/`.
- `pages: write` and `id-token: write`: publish to your GitHub Pages.
- `pull-requests: read` (optional): lets the page footer name Dependabot's
  waiting pull request for a newer forsgren release.
- `issues: write`: lets forsgren open its setup issue (below). Without it the
  run still succeeds; its job summary and the page footer say what is missing.

**The setup issue.** When a forsgren version needs more of your installation (a
permission, a secret, a file or a config key), forsgren opens one issue in this
repository, labelled `forsgren-setup`, listing the steps. It is one issue, not
one per version, and it closes itself once everything is in place. The check
reads the permissions from the forsgren job in `forsgren.yml`, so declare them
explicitly there: `write-all` and `read-all` are not supported, in keeping with
least privilege.

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
- `data/`: your history, written and committed by the daily run as
  `github-actions[bot]`: `deployments.csv`, `commits.csv`, `failures.csv` and
  `issues.csv` (what happened to every issue, for the issues page), plus
  `reach.csv` (how far back each repository has been read) and
  `failures_read.csv` (when its failure issues were last read). The first run
  reads one chunk of `history_chunk_days`; each later run adds one older chunk
  until `history_days` is reached. forsgren never deletes history.

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

What an existing installation must do for a given release is in that
release's notes on the
[forsgren releases page](https://github.com/yveshanoulle/forsgren/releases).

## Licence

The files in this template are under the [0BSD licence](LICENSE): use them
as you like, no attribution needed. forsgren itself is EUPL-1.2.
