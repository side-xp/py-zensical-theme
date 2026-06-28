# Build your own Zensical theme

This guide walks through turning an ordinary [Zensical](https://zensical.org) site into a **reusable, installable theme** that anyone (including CI pipelines) can drop into their own project.

A fresh `zensical new` project is a *site*: a `zensical.toml` plus a `docs/` folder that build into a `site/`. It only renders *its own* content. A *theme*, by contrast, is a small Python package that registers itself with Zensical so that other projects can install it and write a single line of config:

```toml
[project.theme]
name = "your-theme"
```

Zensical discovers installed themes through the `mkdocs.themes` entry point, a mechanism it inherits from [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/). We will build the theme up one step at a time. Throughout the guide, replace the placeholder names with your own:

| Placeholder | Example used here | Notes |
| --- | --- | --- |
| Distribution (pip) name | `your-zensical-theme` | May contain hyphens. |
| Import package / theme dir | `your_theme/` | Python import name (**no** hyphens). |
| Entry-point key (`theme.name`) | `your-theme` | What consumers type in their config. |

> **Prerequisites:** Python 3.11+ and Git. See the project's `CONTRIBUTING.md` for environment setup (virtual environment, etc.).

## Step 1: Create the package skeleton

A theme is a Python package, so it needs three things: build metadata, an importable package directory, and a theme-defaults file.

### 1.1 `pyproject.toml`

At the repository root, declare how the package is built and **crucially** register it under the `mkdocs.themes` entry point:

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "your-zensical-theme"
version = "0.1.0"
description = "My brand theme for Zensical documentation sites"
readme = "README.md"
license = "MIT"
requires-python = ">=3.11"
dependencies = ["zensical"]

[tool.hatch.build.targets.wheel]
# Bundle the whole package, including non-Python data files (.html, .yml, .css).
include = ["your_theme"]

[project.entry-points."mkdocs.themes"]
# key = the name consumers set as `theme.name`; value = the import package.
your-theme = "your_theme"
```

The entry point is the heart of the whole thing: the **key** (`your-theme`) is the name people will type in their `zensical.toml`, and the **value** (`your_theme`) is the package directory that holds your theme files.

### 1.2 `your_theme/__init__.py`

Create the package directory with an **empty** `__init__.py`. Its only job is to mark `your_theme/` as an importable Python package.

### 1.3 `your_theme/mkdocs_theme.yml`

This file holds the theme's **default configuration**. Zensical merges it into every site that uses your theme, so it is where you set sensible defaults that consumers inherit for free (and can still override):

```yaml
# Build on top of Zensical's default theme (the Material look), so you only override what you need to brand it.
extends: material
```

At minimum, `extends: material` tells Zensical your theme builds on its default theme rather than replacing it wholesale.

> ✅ **Checkpoint:** After this step you have an installable, *discoverable* theme, though it doesn't change any styling yet. You can already prove the wiring works by running `pip install -e .` and confirming Zensical accepts `name = "your-theme"` without an "unknown theme" error. Branding comes next.

## Step 2: Add your brand styling

Now make the theme actually *look* like yours. Two pieces work together: a stylesheet that defines your colors, and a template override that loads it on every page.

### 2.1 A brand stylesheet

Create `your_theme/assets/stylesheets/your-theme.css`. Zensical (via Material) exposes its colors as [CSS custom properties](https://zensical.org/docs/setup/colors/#custom-colors), so branding is mostly a matter of setting a handful of variables:

```css
/* Light scheme (the default) */
:root,
[data-md-color-scheme="default"] {
  --md-primary-fg-color:        #4051b5;  /* header, links, primary accents */
  --md-primary-fg-color--light: #5d6cc0;  /* lighter primary variant        */
  --md-primary-fg-color--dark:  #303fa1;  /* darker primary variant         */
  --md-accent-fg-color:         #526cfe;  /* hover / interactive accent     */
}

/* Dark scheme (slate) */
[data-md-color-scheme="slate"] {
  --md-primary-fg-color: #5d6cc0;
  --md-accent-fg-color:  #768cff;
}
```

For these variables to take over, tell the palette you are supplying custom colors. Back in `your_theme/mkdocs_theme.yml`:

```yaml
extends: material
palette:
  - scheme: default
    primary: custom
    accent: custom
```

> 💡 **Tip:** while wiring things up, set a deliberately loud color such as `#FF00FF`. If the page turns magenta, you know the stylesheet is loading; then swap in the real values.

### 2.2 Load the stylesheet with a template override

A bundled stylesheet still has to be linked into the page. Zensical uses [MiniJinja templates](https://zensical.org/docs/customization/), and its base template exposes named **blocks** you can override. Create `your_theme/main.html`:

```jinja2
{% extends "base.html" %}

{% block styles %}
  {{ super() }}
  <link rel="stylesheet" href="{{ base_url }}/assets/stylesheets/your-theme.css" />
{% endblock %}
```

Two details matter here:

- **`{% extends "base.html" %}`** builds on the base theme instead of replacing it.
- **`{{ super() }}`** keeps the base theme's own `<style>`/`<link>` tags, so your stylesheet is added *after* them. Because it loads last, your variables win, without you having to redefine everything the base theme already provides.

> ✅ **Checkpoint:** Your theme now carries its colors with it. Any site that installs the theme and sets `name = "your-theme"` gets the branding automatically, with no extra CSS on their side. We will confirm the stylesheet actually ships in the built site during the verification step.

## Step 3: Use the theme from a demo site

You need a real site to look at while developing the theme. The simplest option is to keep the original `zensical.toml` + `docs/` (the `zensical new` output you started from) and point it at your own theme. This *demo site* doubles as a showcase and a live-preview harness.

### 3.1 Point the site at your theme

In the site's `zensical.toml`, set the theme name to your entry-point key:

```toml
[project.theme]
name = "your-theme"
```

The name only resolves once the theme package is installed in the same environment (`pip install -e .`), which we set up in the next step, so the site won't build until then.

### 3.2 Activate the custom palette

If your site defines its own `palette` (for example to offer a light/dark toggle), remember that a site's palette **replaces** the theme's default palette rather than merging into it. So repeat `primary` and `accent` here, set to `custom`, or your brand variables from Step 2 won't take effect:

```toml
[[project.theme.palette]]
scheme = "default"
primary = "custom"
accent = "custom"
toggle.icon = "lucide/sun"
toggle.name = "Switch to dark mode"

[[project.theme.palette]]
scheme = "slate"
primary = "custom"
accent = "custom"
toggle.icon = "lucide/moon"
toggle.name = "Switch to light mode"
```

> ⚠️ **Heads-up:** Don't run `zensical serve` or `zensical build` yet. Zensical discovers themes only through the installed entry point, so until you install the package (next step) you will get:
>
> ```
> Error: Theme 'your-theme' is not installed. Available themes are:
> ```
>
> That is expected: Step 4 sets up the install that makes the theme resolvable.

## Step 4: Install the theme for local development

The theme becomes discoverable only once its package is installed in the active environment. During development you want an **editable install**, so edits to your templates and stylesheets are picked up without reinstalling every time.

From the repository root, with your virtual environment activated:

```bash
python -m pip install -e .
```

This does two things at once:

- pulls in **Zensical** itself (declared under `dependencies` in `pyproject.toml`), and
- registers your theme's `mkdocs.themes` entry point, so `name = "your-theme"` now resolves.

> ⚠️ The entry point is read **at install time**. If you later edit `pyproject.toml` (for example, rename the theme or change the entry point), re-run `python -m pip install -e .` so Zensical sees the change. Editing templates, `mkdocs_theme.yml`, or CSS does *not* require reinstalling.

### Housekeeping: ignore build artifacts

Working with a Python package produces throwaway files (bytecode caches and packaging build directories). Add them to `.gitignore` so they never get committed:

```gitignore
# Python bytecode caches
__pycache__/
*.py[cod]

# Packaging build artifacts
/build
/dist
*.egg-info/
```

> ✅ **Checkpoint:** `zensical serve` now starts the demo site with your theme and live reload at <http://localhost:8000>, and `zensical build` produces a branded `site/`. The "Theme is not installed" error from the previous step is gone.

## Step 5: Document how others install it

A theme is only reusable if people know how to consume it. The same `pip install` + one config line that you use locally is all anyone else needs, so document it in your `README.md`.

### 5.1 Install from GitHub

If you publish to [PyPI](https://pypi.org), consumers can `pip install your-zensical-theme`. If you don't (or the repo is private), install straight from Git, pinning to a release tag for reproducible builds:

```bash
pip install git+https://github.com/your-org/your-zensical-theme@v0.1.0
```

### 5.2 Enable it

```toml
[project.theme]
name = "your-theme"
```

Because the theme declares `zensical` as a dependency, `pip` installs Zensical automatically: consumers don't list it separately.

### 5.3 Use it in CI

The same two commands drop straight into a pipeline. For GitHub Actions:

```yaml
- name: Install dependencies
  run: pip install git+https://github.com/your-org/your-zensical-theme@v0.1.0

- name: Build documentation
  run: zensical build
```

> 💡 Tag your releases as `vX.Y.Z` and have consumers pin to a tag. They opt into new versions deliberately, and your theme can evolve without breaking their builds unexpectedly.

> ✅ **Checkpoint:** Anyone (including a CI runner) can now turn their Markdown into a branded site with one install and one line of config.

## Step 6: Verify the whole thing works

Before you tag a release, confirm the theme installs, builds, and — most importantly — that your bundled stylesheet actually reaches the output. A packaged theme's `assets/` are copied into the built site automatically, but it's worth proving rather than assuming.

From the repository root, with the virtual environment activated:

```bash
# 1. Install (or reinstall) the theme.
python -m pip install -e .

# 2. Confirm the theme is registered under the entry point.
python -c "from importlib.metadata import entry_points; print([e.name for e in entry_points(group='mkdocs.themes')])"
#   -> ['your-theme']

# 3. Build the demo site.
zensical build

# 4. Confirm your stylesheet shipped and is linked from the pages.
ls site/assets/stylesheets/                 # your-theme.css should be here
grep -r "your-theme.css" site/*.html        # pages should link it
```

If step 4 shows your CSS file in `site/assets/stylesheets/` and the HTML references it, the theme is delivering its branding end to end. Finally, run `zensical serve` and open <http://localhost:8000> to see your colors live.

> 💡 **If the stylesheet does *not* appear** in the output, your Zensical version may not copy theme `assets/` automatically. As a fallback, declare it as default `extra_css` in `mkdocs_theme.yml`, or document a one-line `extra_css` entry for consumers to add. The template-and-palette inheritance still works regardless.

---

That's the whole journey: a `zensical new` site became an installable, brandable theme that anyone can pull into their own documentation with a single line of config. From here you can add a logo and favicon, override header/footer partials, and automate releases — but the foundation is done.
