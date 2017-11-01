# SCION tutorials page

SCION tutorial pages are written in markdown and they are placed in `content` directory. HTML website is generated using [MkDocs](http://www.mkdocs.org/)

Generated HTML website is placed in `docs` directory.

## Prerequisites

In order to build website you will need to [install MkDocs](http://www.mkdocs.org/#installation)
```shell
pip install mkdocs
```
## Building pages

In order to generate website just run 

```shell
mkdocs build
```

Newly generated website will be placed in `docs` directory.

## Development

During the development phase it is possible to run local webserver and automatically refresh website content. To do this run

```shell
mkdocs serve
```

## Adding new page

Adding new page consists of 2 steps:

1. Add new markdown file in `content` directory with page's content
2. Add page information into corresponding category with description in `mkdocs.yml` (at the end of the file)


