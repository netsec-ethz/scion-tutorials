# SCION tutorials page

The website is deployed on [Github Pages](https://netsec-ethz.github.io/scion-tutorials/).

## About

SCION tutorial pages are written in markdown and they are placed in the `content` directory. The HTML website is generated using [MkDocs](http://www.mkdocs.org/).

The generated HTML website is placed in the `docs` directory.

## Prerequisites

In order to build the website you will need to [install MkDocs](http://www.mkdocs.org/#installation):

```shell
pip install mkdocs
```

## Building pages

In order to generate the website just run:

```shell
mkdocs build
```

The newly generated website will be placed in the `docs` directory.

## Development

During the development phase, it is possible to run a local webserver and automatically refresh the website content. To do this, run:

```shell
mkdocs serve
```

## Adding new page

Adding a new page consists of 2 steps:

1. Add a new markdown file in the `content` directory with the page's content
2. Add page information into the corresponding category with description in `mkdocs.yml` (at the end of the file)

## mkdocs-material customisation

The `mkdocs-material` theme is used for the SCION tutorials page.
To change the color palette used in this theme, the source code has to be edited (https://github.com/squidfunk/mkdocs-material/issues/874).

Therefore the mkdocs-material source code was added as a git *subtree*, so we can add commits on top:

    $ git subtree add -P mkdocs-material --squash https://github.com/squidfunk/mkdocs-material.git 3.0.4

Currently, the following customisations have been applied to the theme:

  * Change primary/accent colors
  * Set favicon
  * Set font size and margin for title in navigation-panel
  * Remove next/previous navigation links in footer

Using git subtree will (hopefully) allow to update to newer versions of mkdocs-material if necessary, by running e.g.

    $ git subtree pull -P mkdocs-material --squash https://github.com/squidfunk/mkdocs-material.git <tag>

After updating the mkdocs-material version and re-applying the customisations, the theme has to be rebuilt; you'll need `nodejs>=8` and `npm` installed, then simply run `make` and check in the rebuilt `mkdocs-material/material` folder.

## Check links

You can always look for broken links in the tutorials by running:

```shell
wget --spider -r -nd -nv -l 3 -w 0 -o - https://netsec-ethz.github.io/scion-tutorials/ | grep -B1 'broken link!'
```

Prior to merge to master it is always nice to check against our own repository. For that you need to enable Github Pages in your clone of `scion-tutorials`, and remembering that Github Pages are available only for the master branch, your commits would have to be pushed to your master. E.g.:

```shell
git push myremotename HEAD:master
sleep 30
wget --spider -r -nd -nv -l 3 -w 0 -o - https://mygithubusername.github.io/scion-tutorials/ | grep -B1 'broken link!'
```
