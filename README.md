# forsgren installation

This repository publishes a [forsgren](https://github.com/yveshanoulle/forsgren)
page for your projects, once a day, on GitHub Pages. forsgren measures the four
DORA metrics from data GitHub already has.

Today (forsgren 0.0.1) the page only says "Forsgren 0.0.1": it proves the whole
chain works before any metric is computed.

## Set up

1. **Use this template** (the green button) to create your own repository.
   Private or public, your choice.
2. In your new repository: **Settings → Pages → Source: GitHub Actions**.
3. **Actions → forsgren → Run workflow** once. When it is green, your page is at
   the address Settings → Pages shows.

From then on it runs every day by itself.

## What is in here

- `.github/workflows/forsgren.yml` — the daily run. It calls forsgren's
  reusable workflow at a pinned release.
- `forsgren.config.yml` — your configuration (not read yet in 0.0.1).
- `data/` — where forsgren will keep its history.

## Updating forsgren

In `.github/workflows/forsgren.yml`, change the commit SHA, the `# vX.Y.Z`
comment and `forsgren-version` to the new release, together.

## Licence

The files in this template are under the [0BSD licence](LICENSE): use them
as you like, no attribution needed. forsgren itself is EUPL-1.2.
