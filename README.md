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
