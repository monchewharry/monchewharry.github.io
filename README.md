# [Hugo Résumé Theme](https://github.com/HugoBlox/theme-resume)

- 👉 [**Get Started**](https://hugoblox.com/templates/)
- 📚 [View the **documentation**](https://docs.hugoblox.com/)


## Folder Structure

- content/ for your Markdown-formatted content files (homepage, etc.)
  - _index.md the homepage (Hugo requires that the homepage and archive pages have an underscore prefix)
- assets/
  - media/ for your media files (images, videos)
    - icons/custom/ upload any custom SVG icons you want to use
- config/_default/ for your site configuration files
  - hugo.yaml to configure Hugo (site title, URL, Hugo options, setup per-folder page features)
  - module.yaml to install or uninstall Hugo themes and plugins
  - params.yaml to configure Hugo Blox options (SEO, analytics, site features)
  - menus.yaml to configure your menu links (if the menu is enabled in params.yaml)
  - languages.yaml to configure your site’s language or to set language-specific options in a multilingual site

- static/uploads/ for any files you want visitors to download, such as a PDF of your resume
- hugo-blox/blox/community/ install custom or community blox here
- go.mod sets the version of Hugo themes/plugins which your site uses - learn how to update

## Name Convention

Hugo gives us two options to name standard page files: as `TITLE/index.md` or `TITLE.md` where `TITLE` is your page name.

The page name should be lowercase and using hyphens (-) instead of spaces.

Both approaches result in the same output, so you can choose your preferred approach to naming and organizing files. A benefit to the folder-based approach is that all your page’s files (such as images) are self-contained within the page’s folder, so it’s more portable if you wish to share the original Markdown page with someone.

Hugo requires the homepage and listing pages to be named `_index.md`.