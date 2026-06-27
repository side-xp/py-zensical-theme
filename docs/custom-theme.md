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
