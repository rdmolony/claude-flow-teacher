## Install

- Install `claude-code`

```sh
npm install -g @anthropic-ai/claude-code
```

> [!TIP]
> I run this on [`GitHub Codespaces`](https://docs.github.com/en/codespaces) which I connected to https://github.com/rdmolony/my-codespace-dotfiles to automatically install `claude-code` on initial startup & add some aliases for useful commands

- Fetch submodules

```bash
git submodule update --init --recursive
```

> [!NOTE]
> This repository uses `git` submodules to sync with external repositories which must be manually fetched after cloning this repository

> [!TIP]
> I can avoid this step by passing flag `--recurse-submodules` to `git clone` like `git clone --recurse-submodules <my-repo>`