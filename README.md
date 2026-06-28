# SideXP Zensical Theme

This is a custom Zensical theme, used to generate documentation websites from Markdown files using Sideways Experiments styles.

## Using this theme

This theme is distributed as an installable Python package. Any [Zensical](https://zensical.org) project can use it in three steps.

### 1. Install the theme

Install it straight from GitHub, pinning to a released version tag:

```bash
pip install git+https://github.com/side-xp/py-zensical-theme@v0.1.0
```

> Pinning to a `vX.Y.Z` tag keeps your builds reproducible. Drop the `@v0.1.0` suffix to track the latest `main`.

### 2. Enable it in your `zensical.toml`

```toml
[project.theme]
name = "side-xp"
```

The brand colors ship with the theme, so there is nothing else to configure to get the Sideways Experiments look.

### 3. Build your docs

```bash
zensical build
```

### Continuous integration

In CI, install the theme alongside your other dependencies before building. For example, in a GitHub Actions step:

```yaml
- name: Install dependencies
  run: pip install git+https://github.com/side-xp/py-zensical-theme

- name: Build documentation
  run: zensical build
```

`pip` resolves Zensical itself automatically, since it is declared as a dependency of the theme.

## Contributing

Do you want to get involved in our projects? Check the [CONTRIBUTING.md](./CONTRIBUTING.md) file to learn more!

## License

This project is licensed under the [MIT License](./LICENSE).

---

Crafted and maintained with love by [Sideways Experiments](https://sideways-experiments.com)

(c) 2026 Sideways Experiments