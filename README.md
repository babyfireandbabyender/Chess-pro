# Cheater's Chess on GitHub Pages

The game is one static page (`index.html`) plus the Stockfish 18 engine file (`stockfish-18-asm.js`, about 10 MB), so it runs on GitHub Pages as is. Bots, pass and play, clocks, premoves, castling and game review all work with no setup. The review loads the engine file only when you open a review. Keep `stockfish-18-asm.js` in the same folder as `index.html`.

Accounts need somewhere to store names. On GitHub there is no built-in storage, so this package uses a free Firebase Realtime Database. Without it, accounts still work but are saved only in the browser that created them (the Account panel says so).

## 1. Put the site on GitHub Pages

1. Create a new GitHub repository and upload everything in this folder (`index.html`, `stockfish-18-asm.js`, `config.js`, `database.rules.json`, `.nojekyll`, `README.md`, `THIRD-PARTY.md`).
2. Repository **Settings > Pages**. Under **Build and deployment**, choose **Deploy from a branch**, branch `main`, folder `/ (root)`, then Save.
3. After a minute the game is at `https://YOUR-NAME.github.io/YOUR-REPO/`.

## 2. Turn on worldwide accounts (Firebase)

1. Go to https://console.firebase.google.com and **Add project** (Analytics is not needed).
2. **Build > Authentication > Get started > Sign-in method**, enable **Anonymous**. (This gives each browser a private ID. Players never see it.)
3. **Build > Realtime Database > Create database**. Pick a location and start in **locked mode**.
4. Open the **Rules** tab, paste the contents of `database.rules.json`, and **Publish**.
5. **Project settings (gear icon) > General > Your apps > Add app (the `</>` web icon)**. Register the app and copy the `apiKey` value.
6. On the Realtime Database **Data** tab, copy the database URL (it looks like `https://YOUR-PROJECT-default-rtdb.firebaseio.com`; some regions use `...firebasedatabase.app`).
7. Put both values in `config.js`, commit, and wait for Pages to redeploy.

Optional but recommended: in Google Cloud console, **APIs & Services > Credentials**, restrict that API key to the HTTP referrer `https://YOUR-NAME.github.io/*`. A Firebase web key is not a secret, and the database rules are what protect the data.

## What the accounts do

* A name belongs to one account worldwide, ignoring capitals. The rules make creating a name atomic, so two people creating the same name at the same moment cannot both win.
* Passwords are never stored. Only a salted hash (PBKDF2, 100,000 rounds) is, and it is checked in the browser. Use a strong password: the hash of one account can be read by anyone who knows the name, so a weak password could be guessed.
* Log in with the name and password on any device. The browser remembers you after you close the tab.
* Only the device that created an account can delete it (the rules enforce this). Logging in from another device works, and so does playing.
* There is no password reset. A forgotten password means that name stays reserved.
* Online rooms (the Online mode) currently use Claude's own runtime and do not work on GitHub Pages yet.
