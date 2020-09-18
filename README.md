# The Genji documentation

[![Netlify Status](https://api.netlify.com/api/v1/badges/f36682fc-744b-422a-858c-2df73c85a5a8/deploy-status)](https://app.netlify.com/sites/genjidb/deploys)

This repository contains the assets required to build the [Genji documentation](https://genji.dev).

## Prerequisites

To use this repository, you need the following installed locally:

- [yarn](https://yarnpkg.com/)
- [npm](https://www.npmjs.com/)
- [Hugo](https://gohugo.io/)

Before you start, install the dependencies. Clone the repository and navigate to the directory:

```
git clone https://github.com/genjidb/docs.git
cd docs
```

The Genji website uses the [Docsy Hugo theme](https://github.com/google/docsy#readme) installed as a git submodule.

```
# install dependencies
yarn

# pull in the Docsy submodule
git submodule update --init --recursive --depth 1
```

## Running the website locally using Hugo

To build and test the site locally, run:

```bash
hugo serve
```

This will start the local Hugo server on port 1313. Open up your browser to http://localhost:1313 to view the website.

