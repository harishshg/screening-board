# Screening Question Bank

A one-page board where interview panelists add initial-screening questions, edit them, and vote each one **keep** or **drop**. No account needed: anyone with the link can contribute. Hosted on GitHub Pages, data in Firebase Firestore (free Spark plan).

## Setup (about 15 minutes)

### 1. Create the Firebase project
1. Go to https://console.firebase.google.com → **Add project** (Google Analytics not needed).
2. **Build → Authentication → Get started → Sign-in method → Anonymous → Enable → Save.**
3. **Build → Firestore Database → Create database** → pick a location near your panel (e.g. `asia-south1` for India) → **Start in production mode**.
4. In Firestore, open the **Rules** tab, replace everything with the contents of `firestore.rules`, and click **Publish**.

### 2. Register the web app and copy its config
1. **Project settings (gear icon) → General → Your apps → Web (`</>`)**. Give it any nickname; skip Firebase Hosting.
2. Copy the `firebaseConfig` values (`apiKey`, `authDomain`, `projectId`, `appId`).
3. Open `index.html`, find `const firebaseConfig` at the top of the `<script>`, and paste them in.

The `apiKey` is not a secret. Firebase web keys are meant to be public; the security rules are what protect the data.

### 3. Publish on GitHub Pages
1. Create a repo (e.g. `screening-board`) and push `index.html`, `firestore.rules`, `README.md`.
2. Repo **Settings → Pages → Build and deployment → Source: Deploy from a branch → `main` / root → Save.**
3. After a minute the site is live at `https://<your-username>.github.io/screening-board/`.

### 4. Lock the API key to your site (recommended)
1. Firebase **Authentication → Settings → Authorized domains → Add domain**: `<your-username>.github.io`.
2. In Google Cloud Console → **APIs & Services → Credentials**, open the "Browser key" Firebase created and, under **Website restrictions**, add `https://<your-username>.github.io/*`.

Share the GitHub Pages link with the panel.

## Changing the heading
Edit `TITLE` and `PROMPT` near the top of the script in `index.html`. Each line of `PROMPT` becomes one bullet. Commit and push; Pages updates in about a minute.

## How it works
| Who | Can do |
|---|---|
| Anyone with the link | Read all questions and vote counts |
| Anyone with the link (signed in anonymously by the page) | Add questions, edit any question's wording, vote keep/drop |
| The person who added a question | Remove it |
| You (project owner) | Remove or change anything in **Firestore → Data** in the Firebase console |

- Names are optional. Contributors type one in the composer; it's remembered in their browser. Questions without a name show "Anonymous".
- Votes are one per question per browser. Someone who clears their browser data or switches device gets a fresh vote, so treat counts as a guide for a trusted panel, not a secure ballot.
- Who voted what is stored only by anonymous browser id, never by name.

## Exporting the final list
In the Firebase console, **Firestore → Data → questions** shows every question. For a quick copy, open the live page, sort by **Top**, and copy the list.

## Data model
- `questions/{id}`: `text`, `authorUid`, `authorName`, `createdAt`, and after an edit `edited`, `editedByName`, `editedAt`
- `votes/{uid}`: `{ v: { <questionId>: 1 | -1 } }`
