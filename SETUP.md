# MindSheet — setup guide

Your personal mind-map note app. Infinite canvas, works offline, syncs laptop ↔ phone through your own Google Sheet.

**Files in this package**

| File | What it is |
|---|---|
| `index.html` | The whole app |
| `manifest.webmanifest`, `sw.js`, `icon-192.png`, `icon-512.png` | PWA files (home-screen install + offline) |
| `apps-script.gs` | Backend script you paste into your Google Sheet |
| `SETUP.md` | This guide |

Total setup time: about 10 minutes, one time only.

---

## Step 1 — Create the Google Sheet backend (~4 min)

1. Go to [sheets.new](https://sheets.new) and create a blank spreadsheet. Name it `MindSheet DB`.
2. In the menu: **Extensions → Apps Script**.
3. Delete the sample code in the editor and paste the entire contents of **`apps-script.gs`**.
4. Near the top, replace `PASTE-A-LONG-RANDOM-SECRET-HERE` with your own secret — any long random string (20+ characters, letters/numbers). Keep it somewhere; you'll paste it into the app in Step 3.
5. Click **Deploy → New deployment**.
6. Click the gear next to "Select type" → choose **Web app**, then set:
   - Description: anything
   - **Execute as: Me**
   - **Who has access: Anyone**  ← must be exactly "Anyone" (not "Anyone with a Google account"), otherwise the app can't reach it. Only someone who knows both the URL *and* your secret token can read or write anything.
7. Click **Deploy**, authorize it with your Google account (it will warn that the app is unverified — click *Advanced → Go to … (unsafe)*; it's your own script), then **copy the Web app URL** (ends in `/exec`).

> The script auto-creates three tabs in your Sheet on first sync: `nodes`, `edges`, `maps`. Don't edit them by hand.

## Step 2 — Host the app on GitHub Pages (~4 min)

1. Sign in at [github.com](https://github.com) (create a free account if needed).
2. Click **+ → New repository**. Name: `mindsheet`. Visibility: **Public**. Click **Create repository**.
3. On the new repo page click **uploading an existing file**, drag in these 5 files, and click **Commit changes**:
   `index.html` · `manifest.webmanifest` · `sw.js` · `icon-192.png` · `icon-512.png`
4. Go to **Settings → Pages**. Under "Build and deployment": Source = **Deploy from a branch**, Branch = **main**, folder **/ (root)**. Click **Save**.
5. Wait ~1 minute, refresh the Pages screen — your app URL appears:
   `https://YOUR-USERNAME.github.io/mindsheet/`

> Public repo = the app code is public, which is fine — **your notes are never in the repo**. Notes live only in your browser and your private Google Sheet.

## Step 3 — Connect the app to the Sheet (~1 min)

1. Open your app URL in a browser.
2. Click the **gear** (top right).
3. Paste the **Web app URL** from Step 1 and the **same token** you put in the script.
4. Click **Save**. The status pill in the top bar should turn green: **Synced**. Check your Sheet — the `nodes` tab now has your notes.

## Step 4 — Install on your phone (~1 min)

Open the same URL on your phone, sign-in not needed:

- **Android (Chrome):** menu **⋮ → Add to Home screen → Install**.
- **iPhone (Safari):** **Share → Add to Home Screen**.

Then open it from the home screen icon — full-screen, works with no internet. Enter the same script URL + token in its settings once, and both devices stay in sync.

---

## Using MindSheet

| Action | Laptop | Phone |
|---|---|---|
| New note | double-click canvas, `N`, or the ＋ button | double-tap canvas or ＋ |
| Edit note | double-click the note | double-tap the note |
| Move note | drag it | drag it |
| Link two notes | select a note, drag a round blue dot onto another note | select note → tap 🔗 in the toolbar → tap the target note |
| Pan | drag empty space (or scroll) | drag empty space |
| Zoom | `Ctrl`+scroll, or the −/+ control | pinch |
| Search all maps | `Ctrl+F` or 🔍 | 🔍 |
| Switch / create maps | map name in the top bar | same |
| Delete | select + `Delete`, or 🗑 (with Undo) | 🗑 in toolbar |
| Resize note | drag its bottom-right corner | same |

**Inside a note (markdown):** `# heading`, `**bold**`, `*italic*`, `` `code` ``, `- bullet`, `- [ ] checkbox` (tap to tick), plain URLs become links.

**Sync status pill:** green *Synced* · amber *N pending* (edits queued) · *Offline · N queued* (will push automatically when back online) · tap the pill to force a sync.

---

## How your data flows

```
you edit → saved instantly on the device (IndexedDB) → background sync
        → Google Sheet (nodes / edges / maps tabs) → your other device pulls it
```

- Offline edits queue up and push automatically on reconnect.
- If the same note was edited on both devices while offline, the **newest edit wins**.
- Extra safety: Settings → **Export JSON** downloads a full backup anytime.

## Updating the app later

Replace `index.html` in the GitHub repo (Add file → Upload → Commit). Devices pick the new version up on the second load (the service worker refreshes in the background).

## Troubleshooting

- **Status shows "Sync error"** — tap the pill to retry, then check: token in app matches `TOKEN` in the script exactly; web app deployed with access **Anyone**; URL ends in `/exec`.
- **Edited the script but nothing changed** — Apps Script edits only go live after **Deploy → Manage deployments → ✎ → Version: New version → Deploy**. (The URL stays the same.)
- **"Local only" status** — the app works fine, it just isn't connected to a Sheet yet (Step 3).
- **Phone shows old version** — close and reopen the app twice, or clear site data.
- **Google warned "unverified app" during deploy** — expected; it's your own script running as you. Advanced → continue.
