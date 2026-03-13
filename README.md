# safe-pi

Docker image definition for a coding-friendly development environment based on `node:22-bookworm`.

## What This Image Includes

- Neovim (official stable release, pinned to meet LazyVim requirements)
- LazyVim starter config
- LazyVim plugins pre-synced at build time
- Oh My Zsh with:
  - `agnoster` theme
  - plugins: `fzf-tab`, `git`, `zsh-autosuggestions`, `warhol`, `zshmarks`, `fzf`
- CLI tools commonly useful for coding agents:
  - `ripgrep`, `fd`, `fzf`, `jq`, `tree`, `tmux`, `htop`, `lazygit`, `eza`
  - `python3`, `pip`, `openssh-client`, build toolchain
- `uv` and `uvx` from Astral image
- `@mariozechner/pi-coding-agent` (global npm install)

## Build

```bash
docker build -f Dockerfile.pi -t safe-pi:latest .
```

## Run

```bash
docker run --rm -it -v "$PWD:/workspace" safe-pi:latest
```

Default shell is `zsh`.

## Quick Checks Inside Container

```bash
nvim --version
nvim --headless "+Lazy! health" "+qa"
zsh --version
echo $ZSH_THEME
alias | rg "^(la|ll|lt|l|ls)="
```

## Notes

- `agnoster` prompt renders best with a Nerd Font / Powerline-compatible terminal font.
- LazyVim plugins are installed during image build, so first launch should be ready.
