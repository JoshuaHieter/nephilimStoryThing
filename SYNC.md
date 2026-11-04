# Version control & syncing between devices

The whole folder (the lore bible in `Vault/`, the written chapters, the outline,
and the Quartz site) is one git repo:
**github.com/JoshuaHieter/nephilimStoryThing** (branch `master`).
GitHub holds the master copy, so any device or Claude session can work on it
without the PC being on.

## Who touches it, and how

| Where | How it syncs |
| --- | --- |
| **PC** (`C:\Users\joshu\Documents\Claudework\nephilimStoryThing`) | git: `git pull` before you start, `git add -A && git commit -m "…" && git push` when done |
| **Claude in the cloud** (project threads) | clones from GitHub, commits and pushes back. Doesn't need the PC. |
| **Android phone** | Syncthing ↔ PC only (see `PHONE.md`). Phone edits reach GitHub when the PC commits them. |
| **Another computer** | Clone with GitHub Desktop (or `git clone`), open `Vault/` as an Obsidian vault, pull/push like the PC. |

## The one rule

**Pull before you write, push when you stop.** If two places edit the same file
without syncing in between, git will ask for a merge. That's fixable (ask
Claude), but it's easier to avoid.

When Claude has pushed work from the cloud, run `git pull` on the PC before
editing. Syncthing will then carry those changes to the phone.

## Pushing updates the website

Every push to `master` rebuilds the public site (see `PUBLISHING.md`). Only
`Vault/quartz chapters/` is published. The rest of the vault stays in the repo,
which is **private**.
