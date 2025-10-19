<!--
Guidance for AI coding agents working on this repository (zpyx).
Keep this file short, concrete, and focused on patterns discoverable in the codebase.
--> 

# Copilot instructions — zpyx (Photorama-based Jekyll site)

This repository is a Jekyll-based photo gallery site (Photorama theme) published to GitHub Pages. The site code is static: templates, includes, CSS, JS, and content in `_posts`, `gallery/`, and `journal/` drive the output in `_site`.

Key facts to know before making changes
- Site engine: Jekyll (see `_config.yml`). Output folder: `_site`. Pagination uses `jekyll-paginate`.
- Publishing: GitHub Pages (branch `gh-pages` is used as the site branch in this repo).
- Asset toolchain: `Gruntfile.js` handles `uglify`, `less` and `usebanner` for compilation/minification. `package.json` contains basic metadata only; node packages are not fully specified here.

Where to look for common tasks
- Layout and composition: `_layouts/default.html` and `_includes/*.html` (nav, head, footer, search). Example: search UI is wired in `_includes/search.html`.
- Content: `_posts/` (journal posts) and `gallery/` (albums). Per-post front matter controls tags/categories used by the templates.
- Styles: `css/` (compiled CSS lives here). Less sources (if present) are expected under `less/` and compiled by Grunt.
- Scripts: `js/` contains site scripts. The search feature is implemented in `js/super-search.js` and reads `feed.xml` for posts.

Project-specific patterns and conventions
- All site links and assets use `site.baseurl` and `site.url` from `_config.yml`. When changing templates, prefer using `{{ site.baseurl }}` for asset paths (see `_includes/search.html`).
- Search is client-side: `js/super-search.js` fetches `feed.xml` and expects standard RSS/Feed XML structure. If you modify post front matter that affects feed entries, update `feed.xml` generation or ensure Jekyll includes the fields used (`title`, `link`, `description`, `pubDate`).
- Tags and categories require corresponding files under `journal/tag/` and `journal/category/` to render (see README note). Adding a new tag/category should include the matching md file in those folders.
- Pagination and permalinks are configured in `_config.yml` (paginate: 5, paginate_path). Do not change permalinks without verifying links in `nav` and `index.html`.

Build / dev / deploy workflows (discoverable from repo)
- Local preview with Jekyll (typical):
  - Install Jekyll and required gems (not tracked here). Then run:

```bash
bundle exec jekyll serve --baseurl "{{ site.baseurl }}" --port 4000
```

  - Output appears in `_site/` (see `_config.yml` for `destination`).
- Asset compilation with Grunt (if you change `less` or JS sources):

```bash
# install node deps if needed
npm install
# run default Grunt task to compile/minify assets
grunt
```

- Deployment: this repo uses GitHub Pages. Push changes to branch `gh-pages` and GitHub will serve the site. Ensure `url` and `baseurl` in `_config.yml` point to the correct site root to avoid broken asset paths.

Files to open first when editing behavior/UI
- `_layouts/default.html` — main wrapper, includes search, nav, and footer.
- `_includes/search.html` + `js/super-search.js` — example of client-side search wired to `feed.xml`.
- `_config.yml` — baseurl, url, pagination, excluded files, and comment provider configuration.
- `Gruntfile.js` — if changing asset compilation or introducing new CSS/JS build steps.

Quick rules for automated edits by an AI agent
- Preserve `{{ site.baseurl }}` usage when editing asset links. Hard-coded paths break when `baseurl` changes.
- When editing posts or templates that touch feed fields (title, link, description, pubDate), keep the XML-compatible names used by `super-search.js`.
- Do not remove `journal/tag/` or `journal/category/` index files — templates rely on them to render tag/category pages.
- Run Grunt after changing `less` or unminified JS and run `bundle exec jekyll build` to regenerate `_site` before verifying changes.

Examples from repo to reference in patches
- Wire asset path: `<script src="{{ site.baseurl }}/js/super-search.js"></script>` (`_includes/search.html`).
- Client-side search reads `feed.xml` and expects `channel.item` entries (see `js/super-search.js`).
- Grunt tasks: `uglify` targets `js/<%= pkg.name %>.js` to produce `js/<%= pkg.name %>.min.js` (see `Gruntfile.js`).

If anything is unclear or you need more specifics (Gemfile, exact node dependencies, or how CI builds are configured), ask the maintainer. After edits, regenerate `_site` locally and test navigation and the search box behavior.

---
If you'd like, I can: add a small checklist for PR reviewers, add a Gemfile/package.json with explicit dev dependencies, or convert the Grunt workflow to npm scripts — tell me which and I'll prepare a follow-up patch.
