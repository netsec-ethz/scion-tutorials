# SCION tutorials page

The website is deployed on [Github Pages](https://netsec-ethz.github.io/scion-tutorials/).

## About

SCION tutorial pages are written in markdown and they are placed in the `content` directory (with one exception: the home page is in `./index.md`).

The HTML website is generated using [Jekyll](http://www.jekyllrb.com/) with the [just-the-docs theme](https://pmarsceill.github.io/just-the-docs/).

## Building pages

The webpage is rebuilt automatically when you push to GitHub. If you don't want to see the preview before pushing, you can stop reading now. (Using a Markdown-capable editor, such as the online editor online, might work well for this.)

If you want to preview the docs locally, you need to install the `github-pages` and other
supporting gems (installs Jekyll in the same way as it is installed on GitHub pages):
```shell
make install
```

(You need a working Ruby+bundler install first, install those from your OS's packages. You might have to install the 2.5 version specifically.)

In order to generate the website, run:

```shell
make build
```

The newly generated website will be placed in the `_site` directory.

During the development phase, it is possible to run a local webserver and automatically refresh the website content. To do this, run:

```shell
make serve
```

## Adding a new page

Create a `.md` file inside `content` (or an appropriate subdirectory, if it will have sub-pages). This file must have a [YAML front matter](https://jekyllrb.com/docs/front-matter/), which determines the site navigation structure.

### Top-level page

Create `content/some-topic/index.md` (or just `content/some-topic.md`) with content like:
```md
---
title: Some Topic
nav_order: 47
has_children: true  # omit if you don't want second-level pages
---

This is my content.
```

### Second level page / child page

Create `content/some-topic/some-subtopic.md` with content like:

```md
---
title: Some Subtopic
parent: Some Topic
nav_order: 42
---

This is my content.
```

See https://pmarsceill.github.io/just-the-docs/docs/navigation-structure/ for more info about the site navigation.

## Extras

TODO somebody should upstream these one day.

### Alert/Tip/Note box

```
{% include alert type="Warning" title="DANGER!!1!" content="
I am _very_ **dangerous**!
" %}
```

## Theme customization

We use the [just-the-docs theme](https://pmarsceill.github.io/just-the-docs/) as a "remote theme", which means that the theme source is not included in this repository. However, by copying individual files from the theme to the same place here, we can override them. The theme is very overriding-friendly: see [just-the-docs customization docs](https://pmarsceill.github.io/just-the-docs/docs/customization/)). 

If you are making some generally useful changes, consider opening a PR in the upstream theme instead.

## Check HTML and Links

You can always look for broken links and correct HTML in the tutorials by running:

```shell
make check
```

Prior to merge to master it is always nice to check against our own repository. For that you need to enable Github Pages in your clone of `scion-tutorials`, and remembering that Github Pages are available only for the master branch, your commits would have to be pushed to your master. E.g.:

```shell
git push myremotename HEAD:master
sleep 30
wget --spider -r -nd -nv -l 3 -w 0 -o - https://mygithubusername.github.io/scion-tutorials/ | grep -B1 'broken link!'
```
