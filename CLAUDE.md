# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Jekyll static blog hosted on GitHub Pages. The site lives entirely under the `docs/` directory. Theme is Minima v2.5.1 (pinned by `github-pages` gem) with layout overrides for GTM and AdSense. Content covers CDN, security, networking, and web infrastructure topics.

- **Site URL**: https://brookscunningham.github.io/bcdotcom-blog/
- **Base URL**: `/bcdotcom-blog`
- **Permalink pattern**: `/:year/:month/:title:output_ext`

## Development Commands

All commands must be run from the `docs/` directory:

```bash
cd docs
bundle install            # Install dependencies
bundle exec jekyll serve  # Local dev server (http://localhost:4000/bcdotcom-blog/)
bundle exec jekyll build  # Build static site to docs/_site/
```

## Architecture

- **docs/_config.yml** — Jekyll configuration (theme, permalinks, markdown engine)
- **docs/_posts/** — Blog posts as Markdown files
- **docs/assets/images/** — Post images, organized by article slug
- **docs/index.md** — Home page (layout: home)
- **docs/about.md** — About page (layout: page)
- **docs/Gemfile** — Single dependency: `github-pages` meta-gem (bundles Jekyll + all plugins)
- **docs/_layouts/default.html** — Overrides Minima's base layout to add GTM noscript tag, AdSense footer ad
- **docs/_includes/head.html** — Overrides Minima's head to add GTM and AdSense scripts

## Google Tag Manager

Container ID **GTM-MV2WQ3WW** (shared with www.brookscunningham.com). Implemented via theme overrides:
- `_includes/head.html` — GTM JavaScript snippet in `<head>`
- `_layouts/default.html` — GTM `<noscript>` iframe immediately after `<body>`

## Google AdSense

Client ID **ca-pub-1740016799221875**, ad slot **7281532649** (mirrored from www.brookscunningham.com). Implemented via:
- `_includes/head.html` — `adsbygoogle.js` script with client ID
- `_layouts/default.html` — responsive ad unit in footer (above `{%- include footer.html -%}`)

## Blog Post Conventions

- Filename format: `YYYY-MM-DD-kebab-case-title.md`
- Front matter requires `layout: post`, `title`, and `date` (timezone offset `-0600`)
- Images go in `docs/assets/images/<article-slug>/`
- Image paths in Markdown must use `{{ site.baseurl }}` prefix: `![alt]({{ site.baseurl }}/assets/images/...)`

## Deployment

Push to `main` branch. GitHub Pages builds automatically from the `docs/` directory.
