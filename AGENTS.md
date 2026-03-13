# AGENTS.md

This repository contains a single source of truth for the image: `Dockerfile.pi`.

## Scope

- Primary file to edit: `Dockerfile.pi`
- Supporting docs: `README.md`, `AGENTS.md`

## Agent Goals

- Keep image builds reproducible and non-interactive.
- Preserve first-run developer experience:
  - LazyVim should work on first launch.
  - Oh My Zsh should load with configured theme/plugins.
- Prefer architecture-aware install logic for binaries.

## Required Behaviors

- If changing Neovim setup, keep compatibility with LazyVim (`>= 0.11.2`).
- If changing Oh My Zsh plugins, ensure non-bundled plugins are explicitly installed under:
  - `/root/.oh-my-zsh/custom/plugins/<name>`
- Keep UTF-8 locale environment variables present:
  - `LANG=C.UTF-8`
  - `LC_ALL=C.UTF-8`
- Keep final container command as `CMD ["zsh"]` unless explicitly requested otherwise.

## Validation Checklist

After edits, confirm:

1. Dockerfile syntax is valid.
2. LazyVim bootstrap exists (`git clone https://github.com/LazyVim/starter ...`).
3. Lazy plugin pre-sync exists (`nvim --headless "+Lazy! sync" "+qa"`).
4. `.zshrc` theme/plugins/aliases are configured.
5. Added tools are available from supported architectures.

## Preferred Commands

```bash
docker build -f Dockerfile.pi -t safe-pi:latest .
docker run --rm -it safe-pi:latest
```

Inside container:

```bash
nvim --version
nvim --headless "+Lazy! sync" "+qa"
zsh -i -c 'echo $ZSH_THEME; alias la'
```
