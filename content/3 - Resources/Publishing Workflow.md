---
title: Publishing Workflow
tags:
  - resource
  - quartz
  - workflow
---

How this PARA vault becomes a website. See also [[Example Resource]] for the PARA method itself.

## Pieces

- **Vault:** plain markdown notes in `quartz/content/`, organized as PARA (`Inbox`, `1 - Projects`, `2 - Areas`, `3 - Resources`, `4 - Archive`).
- **Quartz (v5):** static site generator that turns the notes into a website.
- **GitHub repo:** `wyne4rest-afk/inbox-wyne-para` holds Quartz and the notes.
- **GitHub Actions:** builds the site on every push to `main` (`.github/workflows/deploy.yml`).
- **GitHub Pages:** hosts the result at `https://wyne4rest-afk.github.io/inbox-wyne-para/`.

## Flow

1. Add or edit a `.md` file in `content/`.
2. Run `npx quartz sync` from the Quartz folder. It pulls, commits and pushes to `main`.
3. GitHub Actions runs `npx quartz build` and deploys the output to Pages (about a minute).
4. The site updates.

## Requirements

- Node.js v22 or later
- npm v10.9.2 or later
- git
- A GitHub account with the repo above
- Repo visibility: public (GitHub Pages on a private repo needs a paid plan)
- Repo setting: Settings > Pages > Source set to **GitHub Actions**
- GitHub authentication on the machine that pushes (SSH key or `gh auth login`). Never commit tokens or keys.
- `baseUrl` in `quartz.config.yaml` set to `wyne4rest-afk.github.io/inbox-wyne-para`

## What is published

- Everything in `content/` is built into the site, except `Inbox/` and `private/`. Both are listed in `ignorePatterns` in `quartz.config.yaml`.
- The repo is public, so every markdown file in it, including `Inbox/`, is visible on GitHub. Only the website hides those folders. Keep truly private notes out of this repo.

## Commands

```
# first-time setup (already done)
npm i
npx quartz create
npx quartz plugin install --from-config

# check that it builds
npx quartz build

# preview locally (port 8080)
npx quartz build --serve

# publish
npx quartz sync
```

## Not set up yet

- Syncing the vault with Obsidian on other devices (options: Obsidian Git plugin, Syncthing, Obsidian Sync).
