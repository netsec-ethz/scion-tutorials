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


