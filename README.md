# Stock Checklist

Personal pre-trade checklist and trade log. Static HTML/CSS/JS — no backend, no build step.

## Deploy on GitHub Pages

1. Create a new repo on GitHub (public or private — see the note on privacy below).
2. Add these files to it: `index.html`, `.gitignore`, `robots.txt`.
3. Go to **Settings → Pages**, set source to your default branch, root folder, save.
4. Your app is live in a minute or two at `https://<username>.github.io/<repo-name>/`.

## Changing the password (do this before you deploy)

The password is currently set to the placeholder `changeme`. Anyone reading this
README or viewing the page source can see that, so change it first:

1. Open a browser console (right-click anywhere → Inspect → Console tab) on any
   page — it doesn't need to be this app.
2. Paste this, swapping in the password you want, and press Enter:
   ```js
   (function(s){var h=5381;for(var i=0;i<s.length;i++){h=((h*33)^s.charCodeAt(i))>>>0;}return h.toString(16);})("yournewpassword")
   ```
3. It prints a short hex string. Copy it.
4. In `index.html`, find the line near the bottom:
   ```js
   var GATE_HASH = "83e5f1cb"; // hash of "changeme"
   ```
   Replace `83e5f1cb` with the hex string you copied, and update the comment.
5. Save, commit, push.

There's a **Lock** button in the bottom-right corner once you're unlocked — use it
if you're on a shared computer and want to re-lock the page before walking away.

## Restoring your trade data

This repo doesn't contain your trade history — it lives in your browser's local
storage, tied to whichever URL you're using. After you deploy, open the hosted
page, unlock it, then use **Import** and load your `stock-checklist-backup-*.json`
export to bring your trades in. You'll need to do this separately on every
browser/device you use it from — there's no sync between them.

## Surviving a cache/cookie clear: back up to GitHub

The app now has a **GitHub backup** panel (below the trading rules panel) that
saves your entries as a file in a private repo you control — so a cleared
browser cache or cookies won't lose your history. It reads and writes
`stockChecklist_upgrade_entries_v1` directly, the same data the app already
uses.

**One-time setup:**

1. Create a **new private repo** on GitHub, dedicated only to this backup —
   e.g. `stock-checklist-data`. Don't reuse the repo that hosts the app itself.
2. Go to **github.com → Settings → Developer settings → Personal access tokens
   → Fine-grained tokens → Generate new token**.
3. Set an expiration (90 days is a reasonable default — you can generate a new
   one after).
4. Under **Repository access**, choose "Only select repositories" and pick the
   `stock-checklist-data` repo only.
5. Under **Permissions → Repository permissions**, set **Contents: Read and
   write**. Leave everything else as "No access."
6. Generate the token and copy it (GitHub only shows it once).
7. In the app's GitHub backup panel, click **Set up**, paste the token, your
   GitHub username, the repo name, and a file path (default
   `trades-backup.json` is fine). Click **Save settings**.
8. Click **Backup now**. Refresh the repo page on GitHub — the file should
   appear.

From then on: **Backup now** pushes your current entries to that repo (view or
download the file anytime from the GitHub website). **Restore** pulls it back
into whichever browser you're using — handy after a cache clear or on a new
device.

**Auto-backup:** once you're connected, an "Auto-backup after every change"
checkbox appears, checked by default. With it on, any time you add, edit, or
save an entry, the app waits about 4 seconds (to let a burst of edits settle,
so it's not firing on every keystroke) and then pushes the update to GitHub by
itself — no need to remember to click Backup now. Uncheck it if you'd rather
back up manually, e.g. on a slow connection.

**Security notes:**
- The token is scoped to *one* private repo, *contents only* — even in the
  worst case where it leaked, someone could only read/write that one backup
  file, not your account, your other repos, or the app's own repo.
- The token is stored only in this browser's local storage. It is never
  written into `index.html`, never committed, and never sent anywhere except
  directly to `api.github.com` over HTTPS.
- Still, don't set this up on a public/shared computer, and set an expiration
  on the token so it dies on its own if you forget about it.
- If you ever suspect it leaked, revoke it instantly from GitHub's Developer
  settings page — that alone kills it.

## Honest limits of the password gate

This is a **client-side deterrent, not real security**. The page (including the
password hash and the check logic) is fully downloaded to any visitor's browser
regardless of the password — someone comfortable with browser dev tools can
read the source, see the hash, and either brute-force a short password offline
or just disable the check entirely. It will stop casual visitors, search
engines, and people stumbling onto the URL. It will not stop someone who
specifically wants in and knows basic web dev.

The good news: your actual trade data isn't stored anywhere in this file or
this repo, so even a bypass just gets someone an empty tool — not your history.
The one real exposure is if you use the hosted version on a **shared computer**:
once you've imported your data there, anyone else using that same browser could
open local storage and see it. Use the Lock button, or just don't import your
data on a shared machine.

If you want real access control (not just a deterrent), you'd need a host that
supports actual authentication — e.g. Cloudflare Pages with Cloudflare Access,
or Netlify/Vercel password protection (paid tiers) — rather than plain GitHub
Pages.

## Don't commit your backup file

`.gitignore` already excludes `*.json`, so your `stock-checklist-backup-*.json`
export (which has your entry/exit prices and chart images) won't get pushed by
accident. Keep it local, or only in a private repo if you want it backed up
to GitHub.
