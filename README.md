# FieldOps PWA – Desert Link Technologies

## Files
- `index.html` — the entire app (self-contained)
- `manifest.json` — PWA metadata for home screen install
- `sw.js` — service worker for offline support
- `README.md` — this file

---

## Deploy to GitHub Pages (free, 5 minutes)

1. Go to github.com → New repository → name it `fieldops` → Public → Create
2. Upload all 3 files (index.html, manifest.json, sw.js) via the GitHub web UI
3. Go to Settings → Pages → Source: Deploy from branch → Branch: main → / (root) → Save
4. Your app will be live at: `https://YOUR_USERNAME.github.io/fieldops/`

---

## Add to iPhone Home Screen

1. Open the URL above in **Safari** on iPhone (must be Safari, not Chrome)
2. Tap the **Share** button (box with arrow pointing up)
3. Scroll down and tap **"Add to Home Screen"**
4. Name it "FieldOps" → tap **Add**

The app icon will appear on your home screen. It launches full-screen with no Safari UI, just like a native app.

---

## Share with Your Team

Send your techs the same URL. They each do the same "Add to Home Screen" step.
Each device stores its own data locally (localStorage). Data does NOT sync between devices in this version.

---

## Data Backup

Go to Settings → Export Backup (JSON) regularly.
You can also Import a backup JSON to restore data or move to a new device.

---

## Notes
- Data is stored in localStorage tied to the URL origin. As long as you don't clear Safari website data, it persists indefinitely.
- The app works fully offline after first load (service worker caches everything).
