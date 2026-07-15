# safe-pi

Docker image definition for a coding-friendly development environment based on `node:22.23.1-bookworm`.

## What This Image Includes

- Neovim (official stable release, pinned to meet LazyVim requirements)
- LazyVim starter config
- Neovim clipboard defaults:
  - `vim.opt.clipboard = "unnamedplus"`
  - `vim.g.clipboard = "osc52"`
- LazyVim plugins pre-synced at build time
- Oh My Zsh with:
  - `agnoster` theme
  - plugins: `fzf-tab`, `git`, `zsh-autosuggestions`, `warhol`, `zshmarks`, `fzf`
- CLI tools commonly useful for coding agents:
  - `ripgrep`, `fd`, `fzf`, `jq`, `tree`, `tmux`, `htop`, `lazygit`, `eza`
  - `bat`, `termshot`
  - `python3`, `pip`, `openssh-client`, build toolchain
- `uv` and `uvx` from Astral image
- `@earendil-works/pi-coding-agent` `0.80.7` (global npm install)

## Build

```bash
docker build -f Dockerfile.pi -t safe-pi:latest .
```

Pi is pinned for reproducible upgrades. To test another release without editing the Dockerfile:

```bash
docker build \
  --build-arg PI_VERSION=0.80.7 \
  -f Dockerfile.pi \
  -t safe-pi:latest .
```

## Run

```bash
docker run --rm -it \
  -e ANTHROPIC_API_KEY \
  -v "$PWD:$PWD" \
  -w "$PWD" \
  -v safe-pi-agent:/root/.pi/agent \
  safe-pi:latest
```

Omit `-e ANTHROPIC_API_KEY` when using Pi's `/login` flow or another provider. The default shell is `zsh`.

The `safe-pi-agent` volume preserves Pi authentication, settings, trust decisions, packages, and sessions between disposable containers. Avoid mounting the host's `~/.pi/agent` unless the container should have access to those host credentials and sessions.

The project bind mount is writable, so changes inside the mounted directory modify the host project. Mounting it at the same absolute path and setting that path as the working directory gives each project a distinct identity in Pi's trust store. Pi may ask whether to trust a project that contains `.pi` settings or resources; the decision is persisted in the Pi state volume. If you choose another container path, keep it unique per project so a trust decision cannot carry over to an unrelated repository.

To upgrade Pi, change the `PI_VERSION` default in `Dockerfile.pi`, rebuild, and run the checks below. Runtime `pi update` changes only the current container and is discarded with `--rm`.

## Quick Checks Inside Container

```bash
node --version
pi --version
pi --help
nvim --version
nvim --headless "+Lazy! sync" "+qa"
bat --version
termshot --help | head -n 1
zsh -i -c 'echo "$EDITOR"; echo "$ZSH_THEME"; alias la'
```

Expected core versions are Node `22.23.1` and Pi `0.80.7`.

## Pi Migration Notes

- Pi `0.75.0` and newer require Node `>= 22.19.0`; the pinned base image satisfies this requirement.
- In an older persisted `models.json`, replace `compat.sendSessionIdHeader: false` with `compat.sessionAffinityFormat: "openai-nosession"` for Pi `0.80.7`.
- Extension sources that type-check against Pi's previous root API may need to import `@earendil-works/pi-ai/compat`. Pi keeps a runtime compatibility alias for existing extensions.

## Notes

- `agnoster` prompt renders best with a Nerd Font / Powerline-compatible terminal font.
- LazyVim plugins are installed during image build, so first launch should be ready.
- `EDITOR` and `VISUAL` point to Neovim for Pi's external-editor shortcut.
- `vim.g.clipboard = "osc52"` configures Neovim's copy provider. `termshot` clipboard behavior is separate and depends on how `termshot` itself is invoked.
- `termshot --clipboard` may appear on native macOS builds but not in this Linux container build.
