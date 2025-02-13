# Platform.sh User Documentation

This repository holds the public user documentation for Platform.sh.

The documentation site ([docs.platform.sh](https://docs.platform.sh/)) is itself hosted on Platform.sh
and built using the powerful Platform.sh build-and-deploy system.

Every pull request (PR) is automatically built on Platform.sh
and provided with a link to a fully built environment just for that request.
Each PR against the default branch of this repository has a Platform.sh check.
Click **Details** on an open PR to see a fully functional site based on the changes in the PR.
(You can have the same functionality for your repository.)

## Tools

The documentation site is build using [Hugo](https://gohugo.io), a Go static site generator.
The build script is rerun on every deploy to produce a fresh static site instance.

The cross-site search in the documentation is built as a separate Platform.sh app
from the files in the `search` directory using [MeiliSearch](https://www.meilisearch.com/).

## Contributing

Our documentation is public because we want your help in improving and maintaining it.
See our [contribution guidelines](CONTRIBUTING.md) for how to make changes.
All documentation is released under a [Creative Commons Attribution](LICENSE.md) license.

If you spot a problem, open a pull request to fix it!
If you're not sure how, you can also open an issue and we can look into it.

## Running locally

Requires:

* Hugo >= 0.68.3
* Node.js >= 14

### Running locally without search

The documentation and the MeiliSearch search service are separate applications.
It is not necessary to run the MeiliSearch app to build the docs locally,
but if you don't, the search field does not appear in the sidebar.

To run the docs alone, clone this repository
and then install its dependencies and download its example files:

```bash
cd docs
npm install
npm run dev
```

Then build the site,

```bash
hugo serve
```

### Running locally with search

In addition to the above requirements, search also requires:

* [Poetry](https://python-poetry.org/docs/)
* [MeiliSearch](https://www.meilisearch.com/) (see below for installation)

If you would like to test the search server,
you can run it by exporting the `MEILI_MASTER_KEY` environment variable and installing MeiliSearch locally:

```bash
cd search
# Install dependencies for communicating with MeiliSearch.
poetry install
# Download MeiliSearch.
curl -L https://install.meilisearch.com | sh
# Set a master key.
export MEILI_MASTER_KEY=test
# Run it.
./meilisearch
```

In another terminal window, build the React app interface so that search can be pulled into the Hugo site:

```bash
cd ../docs
npm install
npm run dev
npm run build-searchapp
hugo
./deploy.sh
```

> **Note:**
>
> If you receive an error about missing the webpack CLI, you need to install it on your local machine:
>
> ```bash
> npm install webpack-cli -g
> ```

Then update the MeiliSearch server:

```bash
cd ../search
# Export again in this terminal window.
export MEILI_MASTER_KEY=test
# Update the index
./post_deploy.sh
```

Finally, run the site:

```bash
cd ../docs
hugo serve
```
