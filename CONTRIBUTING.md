# Contributing

First of all, thank you for considering contributing to a *Sideways Experiments* project!

At Sideways Experiments, we hate doing the same thing more than once, and we love to share knowledge. Whether because you "just want to help" or share that generous laziness philosophy, you're welcome!

## Developer setup

This project is a custom [Zensical](https://zensical.org) theme. The documentation site is generated from the Markdown files in `docs/` using the `zensical` CLI, and the configuration lives in `zensical.toml`.

### Prerequisites

Before you start, make sure you have the following tools installed:

- [**Python 3.14 or later**](https://www.python.org/downloads)
    - On Windows, tick *"Add python.exe to PATH"* in the installer so the `python` and `pip` commands are available from any terminal.
- [**Git**](https://git-scm.com/downloads)

You can check that both are available by running:

```bash
python --version
git --version
```

### Clone the repository

```bash
git clone https://github.com/side-xp/py-zensical-theme.git
cd py-zensical-theme
```

### Create and activate a virtual environment

We use an isolated [virtual environment](https://docs.python.org/3/library/venv.html) (`.venv`) so the project's dependencies don't interfere with your global Python install. The `.venv` folder is already ignored by Git.

Create it once:

```bash
python -m venv .venv
```

Then activate it (do this every time you open a new terminal in the project):

```powershell
# Windows (PowerShell)
.venv\Scripts\Activate.ps1
```

```bat
:: Windows (Command Prompt)
.venv\Scripts\activate.bat
```

```bash
# macOS / Linux
source .venv/bin/activate
```

> If PowerShell blocks the activation script, allow it for your user with:
> `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned`

Once activated, your prompt is prefixed with `(.venv)`.

### Install the dependencies

With the virtual environment activated, install this project in **editable mode**:

```bash
python -m pip install --upgrade pip
python -m pip install -e .
```

This pulls in Zensical (declared as a dependency in `pyproject.toml`) **and** registers the `side-xp` theme with Zensical, so the demo site in this repo can render with it. Editable mode (`-e`) means changes to the theme's templates and stylesheets are picked up without reinstalling.

> If you change `pyproject.toml` itself (for example its entry points), re-run `python -m pip install -e .` so Zensical picks up the change.

## Previewing and building locally

All commands below assume the virtual environment is **activated** (see above).

### Live preview

Start a local development server with live reload. The site rebuilds automatically as you edit files in `docs/` or `zensical.toml`:

```bash
zensical serve
```

Then open [http://localhost:8000](http://localhost:8000) in your browser.

### Production build

Generate the static site into the `site/` folder (this folder is gitignored and should not be committed):

```bash
zensical build
```

The contents of `site/` are what you would deploy to your documentation host.

## Get involved!

There are many ways to be involved in our open source projects, and there's absolutely no pressure to give more or less of your time. So whether you want to develop the core of a whole package or just post a comment in an issue, any help is appreciated!

So what can you do to help?

- **Discuss with the community**: join our [Discord server](https://discord.gg/bMK2d47JaE), and start chatting with the community or the core team
- **Report bugs**: found a bug when using one of our packages? You can report it in the *Issues* tab on GitHub
- **Suggest improvements**: whether from the [Discord server](https://discord.gg/bMK2d47JaE) or by creating an *Issue*, feel free to talk about your needs or current usage of our solutions, highlight what's missing or what could be better
- **Request new features**: again, you can use the [Discord server](https://discord.gg/bMK2d47JaE) or create a new *Issue* to ask for something new, see if others may need it too, so we can consider modifying an existing package or even start a new project just for it
- **Address issues**: you can contribute directly to the codebase by resolving an issue, creating the required assets, or just implementing changes and create a Pull Request on *GitHub*

> By the way, we love to know how you use our tools, so we can make them better!

## Code of Conduct

*Sideways Experiments* has adopted the [*Contributor Covenant*](https://www.contributor-covenant.org/) as its Code of Conduct for its open source projects, and we expect contributors to adhere to it.

If you observe any unacceptable behavior or need to report a violation, please contact the *Sideways Experiments* core team directly to [contact@sideways-experiments.com](mailto:contact@sideways-experiments.com).

## Code syntax

Please refer to our [general Coding Style specification](https://github.com/side-xp/.github/blob/main/docs/coding-style/README.md) to learn more about the conventions used in this project.

## Submitting a Pull Request

To contribute code:

1. **Fork** this repository
2. Create a **new branch** off `develop`
3. Make your changes and commit them
   - **Signed commits** are required
   - Take care to follow our [commit message guidelines](https://github.com/side-xp/.github/blob/main/docs/coding-style/commit-messages.md)
4. Test your changes locally
5. Submit a **Pull Request targeting `develop` or `dev`**, not `master` or `main`
6. Describe your changes and reference any related issues
7. A member of the ***Sideways Experiments* core team** will review it

As mentioned in the [*Suggesting enhancements or features*](https://github.com/side-xp/.github/blob/main/SUPPORT.md#suggesting-enhancements-or-features) section of our support guidelines, please don't create Pull Requests for unsolicited work.

### Pull Request Requirements

- One PR per logical change (fix, feature, etc.)
- Must pass basic tests (if applicable)
- Must use **signed commits**
- All PRs must be reviewed by a *Sideways Experiments* core team member
- Assign the PR to the core team if not automatically assigned

## Releases

Releases are generated based on `vX.Y.Z` tags pushed to the repository.

Each release will include a compiled package and changelog.

We currently do **not** require contributors to handle release generation (this is done internally).

## License and Contributor Agreement

Most of our projects are licensed under the [MIT License](https://mit-license.org).

A `LICENSE.md` file is always included in our projects' repository root, and all contributions to a specific project will be licensed under the same terms as that file.

We will evaluate whether a Contributor License Agreement (CLA) is required in the future, as the projects go. For now, you can contribute freely under MIT.

## Maintainers

This project is maintained by the team at *Sideways Experiments*.

If you're unsure about any part of the contribution process, feel free to open a discussion on our [Discord server](https://discord.gg/bMK2d47JaE), or by sending an email directly to [contact@sideways-experiments.com](mailto:contact@sideways-experiments.com).

---

Thank you again for being part of the project!