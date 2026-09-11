# BFLIX — personal streaming front-end

Movies, TV, anime, cartoons, K-drama + Arabic. Metadata: Cinemeta, Jikan, TVMaze.
Video: third-party embed servers picked automatically (fastest reachable wins).

## Run locally

```bash
cd movie-player-better && python3 server.py
# → http://localhost:8811  (LAN: http://<your-ip>:8811)
```

## Deploy free (any static host — no backend needed)

**Option A — GitHub Pages (no terminal):**
1. Create a repo on github.com, upload all files (`Add file → Upload files`)
2. Repo Settings → Pages → Deploy from branch → `main` → Save
3. Live at `https://<you>.github.io/<repo>/`

**Option B — Netlify Drop:**
1. app.netlify.com/drop → drag this folder in → instant URL

**Option C — Cloudflare Pages:**
1. dash.cloudflare.com → Pages → Upload assets (drag folder) → instant URL

Notes: everything is relative paths + HTTPS APIs, so it works from any host as-is.
`server.py` is only for local runs — hosts serve the static files directly.

## Cloud accounts (optional, ~10 min, free)

Without this, lists/prefs/history live in each browser (localStorage).
With it, users sign in with Google and everything syncs across devices.

1. console.firebase.google.com → Create project (any name, analytics off is fine)
2. Build → Authentication → Get started → enable **Google** sign-in
3. Build → Firestore Database → Create database (production mode, any region)
4. Firestore → Rules → paste this (users can only read/write their own data):
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{db}/documents {
       match /users/{uid} {
         allow read, write: if request.auth != null && request.auth.uid == uid;
       }
     }
   }
   ```
5. Project settings (gear) → Your apps → Web `</>` → copy the `firebaseConfig` values
   into `firebase-config.js` (replace the PASTE_ME placeholders), commit + push
6. Authentication → Settings → Authorized domains → add your Pages domain
   (`xblackzeus.github.io`) — localhost is allowed by default

Manage users/sessions: Firebase console → Authentication → Users
(see sign-ins, disable or delete accounts). User data: Firestore → `users/{uid}`.
