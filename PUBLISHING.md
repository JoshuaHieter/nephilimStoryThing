# Publishing the draft (Quartz → Cloudflare Pages)

This repo publishes the manuscript as a free, phone-friendly website using
[Quartz v5](https://quartz.jzhao.xyz). Only the chapters are published — the
worldbuilding vault is **not** put on the site.

## How it works

- You write chapters in **`Vault/quartz chapters/`** as numbered files:
  `index.md` (landing page), `0.md` (Prologue), `1.md` (Chapter One), `2.md`, …
- At build time, that folder is copied to **`content/`**, and Quartz turns it
  into a website in **`public/`**.
- Cloudflare rebuilds and redeploys automatically **every time you push to GitHub.**

So the whole update loop is: **edit in Obsidian → `git push` → site updates.**

## Update the live site

```bash
git add -A
git commit -m "new chapter / edits"
git push
```

That's it. Cloudflare does the rest.

## Preview locally (optional)

```bash
rm -rf content && cp -r "Vault/quartz chapters" content
npx quartz plugin install
npx quartz build --serve
```

Then open <http://localhost:8080>.

## One-time Cloudflare Pages setup

1. Make a free account at <https://dash.cloudflare.com/>.
2. **Workers & Pages → Create application → Pages → Connect to Git**, and pick
   the `nephilimStoryThing` GitHub repo (authorize Cloudflare if prompted).
3. In **Set up builds and deployments**, enter:

   | Setting | Value |
   | --- | --- |
   | Production branch | `master` |
   | Framework preset | `None` |
   | Build command | `rm -rf content && cp -r "Vault/quartz chapters" content && npx quartz plugin install && npx quartz build` |
   | Build output directory | `public` |

4. Under **Environment variables**, add: `NODE_VERSION` = `22`
   *(Quartz needs Node 22+; Cloudflare's default is older.)*
5. **Save and deploy.** In ~a minute you get a `https://<name>.pages.dev` URL —
   share that with anyone. Works on any phone, no account needed to read.

### After the first deploy
- Copy the real `xxx.pages.dev` URL into `quartz.config.yaml` → `baseUrl:`
  (replacing the placeholder), commit, and push. (Improves link previews/RSS.)

## Keeping it private to just friends (optional)

The site is public by default. To gate it behind a login:
- **Cloudflare Zero Trust → Access → Applications**, add a self-hosted app for
  your `pages.dev` domain, and allow only specific emails. **Free for up to 50 users.**
  Friends get a one-time email code to view.

## Notes
- `content/`, `node_modules/`, `public/`, `.quartz/` are build artifacts.
  `content/` is committed (a copy of the chapters) because Quartz's file scan
  respects `.gitignore`; it's refreshed on every build.
- **Repo visibility:** the whole worldbuilding vault lives in this repo. If the
  repo is **public**, those spoilers are visible on GitHub (the *website* still
  only shows chapters). Keep the repo **private** if that matters.
