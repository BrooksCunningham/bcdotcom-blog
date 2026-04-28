# bcdotcom-blog

Personal tech blog by [Brooks Cunningham](https://www.brookscunningham.com), covering CDN, security, networking, and web infrastructure topics. Built with [Jekyll](https://jekyllrb.com/) and hosted on [GitHub Pages](https://pages.github.com/).

**Live site:** https://www.brookscunningham.com

---

## Getting Started

All site files live under the `docs/` directory.

### Prerequisites

- Ruby (version compatible with the `github-pages` gem)
- Bundler (`gem install bundler`)

### Local Development

```bash
cd docs
bundle install
bundle exec jekyll serve
```

The site will be available at `http://localhost:4000/`.

### Build

```bash
cd docs
bundle exec jekyll build
```

Static output is written to `docs/_site/`.

---

## Project Structure

```
docs/
├── _config.yml          # Jekyll configuration (theme, permalinks, markdown engine)
├── _includes/
│   └── head.html        # Overrides Minima's <head> — adds GTM & AdSense scripts
├── _layouts/
│   └── default.html     # Overrides Minima's base layout — adds GTM noscript & AdSense footer ad
├── _posts/              # Blog posts as Markdown files (YYYY-MM-DD-kebab-title.md)
├── assets/
│   └── images/          # Post images, organised by article slug
├── about.md             # About page (layout: page)
├── index.md             # Home page (layout: home)
├── Gemfile              # Single dependency: github-pages meta-gem
└── favicon.ico
```

---

## Writing a Post

1. Create a file in `docs/_posts/` named `YYYY-MM-DD-kebab-case-title.md`.
2. Add the required front matter:
   ```yaml
   ---
   layout: post
   title:  "Your Post Title"
   date:   YYYY-MM-DD HH:MM:SS -0600
   ---
   ```
3. Place any images in `docs/assets/images/<article-slug>/` and reference them with the `{{ site.baseurl }}` prefix:
   ```markdown
   ![Alt text]({{ site.baseurl }}/assets/images/article-slug/image.png)
   ```

---

## Deployment

Push to the `main` branch. GitHub Pages automatically builds and publishes from the `docs/` directory.

---

## License

See [LICENSE](LICENSE).
