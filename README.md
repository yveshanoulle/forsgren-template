# forsgren installation

This repository publishes a [forsgren](https://github.com/yveshanoulle/forsgren)
page for your projects, once a day, on GitHub Pages. forsgren measures the four
DORA metrics from data GitHub already has.

Today (forsgren 0.0.2) the page only says "Forsgren 0.0.2", and every daily run
first checks `forsgren.config.yml`. No metric is computed yet.

## Set up

1. **Use this template** (the green button) to create your own repository.
   Private or public, your choice.
2. Replace the acme example in `forsgren.config.yml` with your own projects
   and repositories.
3. In your new repository: **Settings → Pages → Source: GitHub Actions**.
4. **Actions → forsgren → Run workflow** once. When it is green, your page is at
   the address Settings → Pages shows.

From then on it runs every day by itself.

## What is in here

- `.github/workflows/forsgren.yml` — the daily run. It calls forsgren's
  reusable workflow at a pinned release.
- `forsgren.config.yml` — your configuration, checked before every run.
- No `data/` yet: forsgren creates it when it first stores history, and never
  overwrites history that is there.

## Updating forsgren

Change only the `uses:` line in `.github/workflows/forsgren.yml`, in this
repository: the commit SHA and the `# vX.Y.Z` comment, to the new release,
together. Never re-create the repository from the template to update: that
loses `data/` and your secrets.

## Licence

The files in this template are under the [0BSD licence](LICENSE): use them
as you like, no attribution needed. forsgren itself is EUPL-1.2.
