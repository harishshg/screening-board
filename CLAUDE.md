# Screening Question Bank

A single-page web app where interview panelists add initial-screening questions, edit them, and vote each one keep or drop. Contributors need no account. Hosted on GitHub Pages; data lives in Firebase Firestore.

## Layout
- `index.html`: the whole app. Inline CSS, then markup, then one `<script type="module">`.
  - Top of the script: `firebaseConfig` (paste from Firebase console) and the board text `TITLE` / `PROMPT` (each PROMPT line is one bullet).
  - Firebase JS SDK v10.12.2 loaded as ES modules from `www.gstatic.com`. No build step, no npm.
- `firestore.rules`: Firestore security rules. Deploy whenever changed with `firebase deploy --only firestore:rules` (or paste into Firebase console → Firestore → Rules). Keep them in sync with the fields the app writes.
- `firebase.json` / `.firebaserc`: Firebase CLI config; the project is `screening-board-hs98`.
- `README.md`: setup and deploy steps for humans.

## Data model
- `questions/{id}`: `text` (1–400 chars), `authorUid`, `authorName` (≤60, optional), `createdAt` (server timestamp); after an edit also `edited: true`, `editedByName`, `editedAt` (server timestamp).
- `messages/{id}`: Discussion chat below the list. `text` (1–500 chars), `authorUid`, `authorName` (≤60, optional), `createdAt` (server timestamp). Only the author can delete; the page shows the latest 300. Rendered by `renderChat()`, separate from `render()`.
- `votes/{uid}`: `{ v: { <questionId>: 1 | -1 } }`, one doc per anonymous user, written only by that user. Tallies are computed client-side from the whole `votes` collection.

## Auth and permissions
- The page calls `signInAnonymously` on load; each browser gets a stable anonymous uid.
- Anyone can read. Signed-in (anonymous) users can create questions under their own uid, edit any question's text, and vote. Only the author can delete; the project owner moderates from the Firebase console.

## Conventions
- Plain JS and DOM APIs; render by rebuilding the list in `render()`. `render()` skips while an inline edit is open (`editingId`) so live snapshots don't wipe the textarea; call `render(true)` to force.
- All colors are CSS tokens on `:root` with dark-mode overrides via `prefers-color-scheme` and `[data-theme]`. Use tokens, not literal colors.
- Must stay usable at ~400px width.
- Writes go through Firestore SDK calls; any new field must also be allowed in `firestore.rules`.

## Testing locally
Serve the folder over HTTP (ES modules don't load from `file://`):
```
python3 -m http.server 8000
```
Then open http://localhost:8000. Add `localhost` to Firebase Authentication → Settings → Authorized domains if sign-in fails locally.

## Deploy
Commit and push to `main`; GitHub Pages serves the repo root.
