# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Solar Theme for Hugo is a minimalistic blog theme, forked from Solar Theme for Ghost. It's a pure Hugo theme with no server-side logic or build system beyond Hugo itself.

## Development Commands

### Running the Example Site

```bash
# From the exampleSite directory
cd exampleSite
hugo server --themesDir ../.. --theme solar-theme-hugo

# Or from the root directory
hugo server -s exampleSite
```

### Testing in a Real Site

```bash
# From a Hugo site that uses this theme
hugo server --theme solar-theme-hugo
```

## Architecture

### Color Scheme System

The theme uses a modular color scheme system built on SCSS imports:

- **Base layout**: `assets/css/layout.scss` contains all structural styles and reset CSS
- **Color schemes**: Four separate color scheme files that import layout.scss:
  - `colors-dark.scss` - Solarized Dark (default)
  - `colors-light.scss` - Solarized Light
  - `colors-gray.scss` - Gray variant
  - `colors-preference.scss` - Uses `@media (prefers-color-scheme)` to switch between light/dark based on user's system preference

The color scheme is selected dynamically in `layouts/_default/baseof.html:13` using:
```go
{{ $style := resources.Get (printf "css/colors-%s.scss" (.Site.Params.scheme | default "dark")) | toCSS | minify | fingerprint }}
```

This means the `scheme` parameter in `config.toml` determines which SCSS file is loaded.

### Template Structure

- **Base template**: `layouts/_default/baseof.html` defines the overall HTML structure with header, sidebar, content area, and footer
- **Content templates**:
  - `layouts/_default/single.html` - Individual post/page rendering
  - `layouts/_default/list.html` - List pages (homepage, archives)
- **Partials**:
  - `layouts/partials/head.html` - Custom HTML injection point before `</head>` (empty by default)
  - `layouts/partials/foot.html` - Custom HTML injection point before `</body>` (empty by default)
  - `layouts/partials/navigation.html` - Sidebar navigation
  - `layouts/partials/pagination.html` - Pagination controls

### Customization Points

Users customize the theme by:

1. **Color scheme**: Set `params.scheme` in `config.toml` to "light", "dark", "gray", or "preference"
2. **Logo**: Override default logo by placing `logo.svg` in the site's `static/img/` directory (referenced in baseof.html:272)
3. **Custom HTML**: Create `head.html` or `foot.html` in the site's `layouts/partials/` directory to inject additional HTML (for analytics, JavaScript syntax highlighters like highlight.js, etc.)

### Syntax Highlighting

The theme intentionally does NOT include syntax highlighting. Users choose between:

1. **Hugo's built-in**: Configure `pygmentsStyle` in config.toml to match color scheme (e.g., "solarized-dark" for dark scheme)
2. **JavaScript highlighter**: Include via custom `foot.html` partial

The theme includes Prism.js token styles in layout.scss:552-614 for compatibility.

## Important Files

- `theme.toml` - Theme metadata, requires Hugo >= 0.25.1
- `exampleSite/config.toml` - Reference configuration showing all theme parameters
- `layouts/_default/baseof.html` - Main template that all pages use
- `assets/css/layout.scss` - All structural and base styles
- `assets/css/colors-*.scss` - Color scheme variants

## Layout Notes

The theme uses absolute positioning for the sidebar (layout.scss:319-325):
- Desktop: Sidebar is positioned at `left: -240px` relative to the main content
- Mobile (< 870px): Sidebar becomes relative positioned above content (layout.scss:480-507)

Main content width is fixed at 500px on desktop, becomes fluid on mobile.
