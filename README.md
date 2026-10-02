# forsgren installation

This repository publishes a [forsgren](https://github.com/yveshanoulle/forsgren)
page for your projects, once a day, on GitHub Pages. forsgren measures the four
DORA metrics from data GitHub already has.

Today (forsgren 0.0.2) the page only says "Forsgren 0.0.2", and every daily run
first checks your `forsgren.config.yml`. No metric is computed yet.

This template holds only forsgren's own files. Your configuration and your
history are yours: they are never in the template, so installing a new
version never overwrites them.

## Set up

1. **Use this template** (the green button) to create your own repository.
   Private or public, your choice.
2. In it, create `forsgren.config.yml` at the top level with your own
   projects and repositories, for example:

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
   refuses a missing or invalid file with a one-line message.
3. **Settings → Pages → Source: GitHub Actions**.
4. **Actions → forsgren → Run workflow** once. When it is green, your page is at
   the address Settings → Pages shows.

From then on it runs every day by itself.

## What is in here

- `.github/workflows/forsgren.yml`: the daily run. It calls forsgren's
  reusable workflow at a pinned release.
- `README.md` and `LICENSE`: this text and its licence.

What you add, and what forsgren never overwrites:

- `forsgren.config.yml`: your configuration.
- `data/`: your history. forsgren creates it when it first stores history and
  never overwrites history that is there.

## Installing a new version

Copy the files of the new forsgren-template over your repository. That
replaces forsgren's files (the workflow, with its new pinned release, and
this README) and leaves `forsgren.config.yml` and `data/` as they are. Your
repository keeps its secrets and its Pages address.

## Licence

The files in this template are under the [0BSD licence](LICENSE): use them
as you like, no attribution needed. forsgren itself is EUPL-1.2.
