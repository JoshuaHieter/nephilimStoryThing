# Phone sync (Syncthing)

The vault at `Vault/` syncs to an Android phone with Syncthing, so chapters can be
written in Obsidian on the phone. Free, no cloud account, no git on the phone.

## What syncs

- **Everything in `Vault/`** — chapters, lore, `.obsidian/` settings — so wikilinks
  resolve on the phone. Folder ID `nephilim-vault`.
- **Not synced** (`Vault/.stignore`):

  ```
  .obsidian/workspace.json
  .obsidian/workspace-mobile.json
  .trash
  ```

  The workspace files are per-device UI state (open tabs, pane layout) and are also
  git-ignored. `.trash` is Obsidian's local recycle bin.
- **Git stays on the PC.** Phone edits arrive in this checkout as ordinary
  working-tree changes; commit them from the PC (or a Claude session) as usual.
  The Quartz site files outside `Vault/` are not part of the sync.

## PC side (already done)

- Syncthing 2.x installed with `winget install Syncthing.Syncthing` (portable build
  under `%LOCALAPPDATA%\Microsoft\WinGet\Packages\`).
- Starts with Windows via a shortcut in the user Startup folder
  (`shell:startup` → `Syncthing.lnk`, runs `syncthing serve --no-browser --no-console`).
- Web UI: <http://localhost:8384> (bound to localhost only).
- Folder `nephilim-vault` → `Vault/`, filesystem watcher on, **trash-can file
  versioning** (30 days) as insurance against a phone-side delete. Deleted or
  overwritten files land in `Vault/.stversions/`, which Syncthing does not sync.

## Phone side (Android)

1. **Install Syncthing-Fork.** The official Syncthing Android app was discontinued
   in Dec 2024. Get *Syncthing-Fork* (by Catfriend1) from F-Droid, or the APK from
   its GitHub releases page.
2. **Grant "All files access"** when the app asks (Settings → Apps → Syncthing-Fork
   → All files access if you missed the prompt). Also allow it to ignore battery
   optimisation so it keeps syncing in the background.
3. **Pair with the PC.** On the PC web UI: *Actions → Show ID* shows a QR code. On
   the phone: *Devices → + → scan QR*. Give it a name like "PC". Back on the PC web
   UI a banner appears: *"New device … wants to connect"* → **Add Device**, and in
   that dialog tick the folder **Nephilim Vault** under *Sharing*.
4. **Accept the folder on the phone.** A notification / banner offers the shared
   folder `nephilim-vault`. Accept it and set the phone-side path to a folder
   Obsidian can open, e.g. internal storage → `Documents/NephilimVault`. Wait for
   the first sync to finish (folder shows *Up to Date*).
5. **Open it in Obsidian.** Obsidian → *Open folder as vault* → pick that folder.
   Obsidian will create `.obsidian/workspace-mobile.json` locally; it's ignored.

To add another phone or a second PC, repeat these steps; every device pairs with
the PC.

## Rules of the road

- **Don't edit the same chapter on both devices at the same time.** Syncthing
  never overwrites a newer edit with an older one; instead it keeps both copies.
  Finish on one device, let it sync (seconds when both are online), then switch.
- **The PC must be on** (and Syncthing running) for a sync to happen. There is no
  server in between. Edits made on the phone while the PC is off are safe and land
  the next time both are up.
- Obsidian on the phone should be closed or on another note while a large sync
  lands, otherwise it may hold a stale copy in its editor.

## If a `.sync-conflict` file appears

You'll see something like

```
Written Chapters/Chapter 5.sync-conflict-20260922-101530-ABCDEFG.md
```

next to the original. Both edits were kept: the original file holds the version
that "won" (the later modification time), the conflict file holds the other. To
resolve, on the PC:

1. `git diff` shows what the winning version changed against the last commit.
2. Diff the conflict file against the original, merge the missing bits into the
   original by hand (or ask a Claude session to do the merge).
3. Delete the `.sync-conflict-*` file. Never commit one.

Syncthing also keeps the losing side in `.stversions/` on the PC, so nothing is
lost even if the conflict file is deleted by mistake.

## Open questions

- **The GitHub repo is public** (checked 2026-09-22: the repo page loads without
  signing in). This doesn't change the sync design (the phone never touches git),
  but it means every committed chapter is world-readable. Decide whether that's
  intended; the Quartz site presumably publishes some of it on purpose.
- The Syncthing web UI has no username/password set. It only listens on
  `127.0.0.1`, so nothing off this PC can reach it. Set one under *Actions →
  Settings → GUI* if other people use this Windows account.
