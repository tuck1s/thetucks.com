# thetucks.com static site

[![Deploy Hugo site to Pages](https://github.com/tuck1s/thetucks.com/actions/workflows/hugo.yaml/badge.svg)](https://github.com/tuck1s/thetucks.com/actions/workflows/hugo.yaml)

## Structure

This site uses the [Ananke theme](https://github.com/gohugo-ananke/ananke), pulled in as a
[Hugo Module](https://gohugo.io/hugo-modules/) (see [`go.mod`](go.mod)) rather than a git submodule — no
`--recurse-submodules` or `git submodule update` step is needed when cloning.

```
content
  post
    postname
      index.md
      img
        img1.jpg
        : etc
```

## Setup

Requires [Hugo (extended)](https://gohugo.io/installation/) and [Go](https://go.dev/dl/) (used by Hugo Modules to
fetch the theme).

## Local development

In project folder, run `hugo server` and open locally in browser. The first run downloads the theme module into
the local Go module cache.

## Production build

Build on checkin is automatically done using a standard Github Action for Hugo sites
([`.github/workflows/hugo.yaml`](.github/workflows/hugo.yaml)).
