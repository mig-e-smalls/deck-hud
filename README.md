# Deck HUD

A table-side HUD for Commander games. It runs entirely in the browser, so there is no server to maintain and nothing to pay for.

**Files in this folder**

- `index.html` is the whole app.
- `manifest.json`, `icon-*.png`, `icon.svg` and `sw.js` let people install it on their phone and use it offline.
- `database.rules.json` holds the security rules for the Table feature (step 3).

**What works with no setup**

- Trackers, the turn checklist, cards in play, mana and creatures.
- The opponents panel and the glossary.
- Custom decks, saved on each phone.
- Decklist import. It looks every card up on [Scryfall](https://scryfall.com/docs/api), which is free and needs no key.
- The full precon library from MTGJSON.

**What needs one-time setup:** the Table panel, where everyone sees each other's boards live. Step 3 covers it and takes about 10 minutes.

---

## 1. Put it online with GitHub Pages (free)

1. Make a free account at [github.com](https://github.com) if you don't have one.
2. Click **+ → New repository**, name it `deck-hud`, set it to **Public**, and click **Create repository**.
3. On the new repo page, click **uploading an existing file**. Drag in every file from this folder and click **Commit changes**.
4. Go to **Settings → Pages**. Under **Build and deployment**, choose **Deploy from a branch**, then select `main` and `/ (root)`, and click **Save**.
5. After a minute or two, the site is live at `https://YOUR-USERNAME.github.io/deck-hud/`.

Netlify or Vercel work too: drag the folder onto their dashboard.

## 2. Install it on phones

- **iPhone:** open the link in Safari, tap **Share**, then **Add to Home Screen**.
- **Android:** open the link in Chrome, tap **⋮**, then **Install app** (or **Add to Home screen**).

The app opens full screen and keeps the screen awake during a game.

## 3. Turn on the Table feature (Firebase, free)

Skip this step if you only want the single-player HUD.

1. Go to [console.firebase.google.com](https://console.firebase.google.com) and click **Create a project**. Any name works, and you can turn Google Analytics off. The free **Spark** plan is all you need.
2. **Turn on anonymous sign-in.** Go to **Build → Authentication → Get started → Sign-in method**, then enable **Anonymous** and click **Save**. Each phone gets an anonymous ID, so nobody needs an account.
3. **Create the database.** Go to **Build → Realtime Database → Create database**. Pick a location near you and choose **Start in locked mode**.
4. **Add the rules.** In the database's **Rules** tab, replace everything with the contents of `database.rules.json` and click **Publish**. These rules let each phone write only its own board and let signed-in players read the table.
5. **Get your config.** Click the gear icon and go to **Project settings → Your apps**. Click the **Web** icon (`</>`), give it any nickname, and click **Register app**. Firebase shows a `firebaseConfig` block; copy its `apiKey`, `authDomain`, `databaseURL`, `projectId` and `appId`.
6. **Paste the config into the app.** Open `index.html`, find `window.FIREBASE_CONFIG` near the top, fill in those five values and save.
7. **Allow your site's address.** Go to **Authentication → Settings → Authorized domains**, click **Add domain**, and enter `YOUR-USERNAME.github.io`.
8. Upload the updated `index.html` to GitHub, the same way as step 1.3. Choose to replace the old file.

The Firebase `apiKey` isn't a secret. It only identifies your project, and the database rules are what protect it.

The free plan allows 100 phones connected at once, which is plenty for a game store night.

## Using it

- **Any Commander precon:** tap **Browse all Commander precons**, search by name (for example "Necron"), and tap the deck. The app downloads the full list with every card's rules text from [MTGJSON](https://mtgjson.com), a free public card database, and sets up trackers, card effects, mana, token types and colors on its own. The deck is then saved under My decks, so it opens instantly next time.
- **Hand-built precons:** these have checked reminders and card categories. Tap **Put into play** when a card lands. Its effects, team buffs, mana and reminders then switch on.
- **Your own deck:** tap **New deck** and paste a decklist from Moxfield or Archidekt (Export → Text). Then tap **Import from decklist**. The app reads each card's rules text from Scryfall and builds:
  - trackers such as energy, treasure and monarch
  - card reminders sorted by when they happen
  - mana sources
  - token types
  - team buffs

  Put your commander's exact name in the Commander field first, so its triggers always show. Check the result, since the import is automatic and won't be perfect for every card.
- **Table:** one person taps **New code** and everyone joins that code with their name. Each phone shares its own board, and you can tap a player to see their creatures, cards in play and effects.

All decks and games are saved in each phone's browser. Clearing the browser's site data erases them.

## Updating the app

Edit `index.html` and upload it to GitHub again. Phones pick up the new version the next time they open the app while online.
