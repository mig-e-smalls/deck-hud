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

## 4. Keep the precon library on your own site (recommended)

A weekly GitHub job downloads every Commander precon from MTGJSON and saves it into this repo under `precons/`. The app reads decks from there first and only asks MTGJSON directly if those files are missing. That way, search works even if MTGJSON changes how its site works.

1. In your GitHub repo, click **Add file → Create new file**.
2. For the file name, type `.github/workflows/update-precons.yml`. The slashes create the folders automatically.
3. Paste in the contents of `update-precons.yml` and click **Commit changes**.
4. Go to **Settings → Actions → General**. Under **Workflow permissions**, choose **Read and write permissions** and click **Save**.
5. Open the **Actions** tab, click **Update precons** on the left, then click **Run workflow**. It takes a minute or two. A green check means a new `precons` folder is in your repo, and Netlify redeploys on its own.

After that, it runs every Monday on its own. If a run ever finds fewer than 50 decks, it stops and leaves the last good copy in place.

## 5. Bug reports, tips, and the TV view

- **Bug reports.** The Report a bug link at the bottom of the app sends reports to Netlify Forms, which are free on your plan. Turn them on once: in Netlify, open your project, go to **Forms**, and click **Enable form detection**. Then redeploy. Reports show up under **Forms → bug-report**, and you can set email notifications there.
- **Tip button.** Open `index.html`, find `const TIP_URL=""` and paste your Ko-fi or Buy Me a Coffee link between the quotes. Wizards' Fan Content Policy allows donations as long as the app stays free to use, so keep it free.
- **Table view for a TV.** In a room, tap **Table view**. It opens a watch-only screen laid out like a tabletop seen from above: players sit on both sides of a felt table, in turn order, with their creatures facing the middle and their life totals at the outer edge.
  - On an iPad with Stage Manager and an external display, drag that window onto the TV, and the iPad stays on your own board.
  - Or open `https://mtghud.netlify.app/#tv` on any device connected to the TV, like a laptop over HDMI or a smart TV browser, and enter the room name and key.
  - The table view never changes anything in the room.

## Play modes, the shared table and sessions

- **Modes** (pick on the home screen): **AR companion** tracks what helps while you play with real cards. **Full sim, real cards** adds hand and library counts with Draw, Play a land, Cast, Discard and Mill buttons, plus Take control and Copy on other players' cards. **Full sim, no cards** shuffles and deals your deck in the app; your hand stays on your phone and others only see how many cards you hold.
- **Shared table** (in a room): one panel shows whose turn it is, who holds Monarch and Initiative, and Day/Night. Pass turn hands the turn to the next player in the order people joined.
- **Sessions:** in a room, tap **Start a session** to keep score across games, run a waiting line, and show standings on the TV between games.
- **These need the latest `database.rules.json` published in Firebase.** It adds a small shared `state` area for each room.
- `docs/PROJECT_LOG.md` has the roadmap, review checklists and known limits; `docs/DECISIONS.md` explains why things work the way they do.

## Security notes

- Rooms are a room name plus a secret key. The app hashes both into the database address, so the room name alone can't find a room.
- **Update your Firebase rules** to the new `database.rules.json`. It only allows those hashed room addresses, lets each phone write only its own board, and caps field sizes. Paste it in **Realtime Database → Rules** and click **Publish**.
- `_headers` adds standard security headers on Netlify.
- In no-cards mode, your hand and library order are saved only in your own browser and are never sent anywhere.
- The app stores only a nickname and board state. There are no accounts or emails, and saved decks stay in each browser.

## Using it

- **Any Commander precon:** tap **Browse all Commander precons**, search by deck or commander name (for example "Necron" or "Szarekh"), and tap the deck. The app downloads the full list with every card's rules text from [MTGJSON](https://mtgjson.com), a free public card database, and sets up trackers, card effects, mana, token types and colors on its own. The deck is then saved under My decks, so it opens instantly next time.
- **Hand-built precons:** these have checked reminders and card categories. Tap **Put into play** when a card lands. Its effects, team buffs, mana and reminders then switch on.
- **Your own deck:** tap **New deck** and paste a decklist from Moxfield or Archidekt (Export → Text). Then tap **Import from decklist**. The app reads each card's rules text from Scryfall and builds:
  - trackers such as energy, treasure and monarch
  - card reminders sorted by when they happen
  - mana sources
  - token types
  - team buffs

  The app finds the commander automatically when the list marks it. That covers Moxfield and MTG Arena exports with a "Commander" section, Archidekt's [Commander] tag, and MTGO lists that end with the commander on its own line. If it can't tell, it asks you to type the name. Check the result, since the import is automatic and won't be perfect for every card.
- **Private rooms:** one person taps **Create a room**, names it, and gets a key. Tap **Invite** to text it, or Copy invite. Everyone else joins with the room name and key, or opens the invite link. Each phone shares its own board under Other players.

All decks and games are saved in each phone's browser. Clearing the browser's site data erases them.

## Updating the app

Edit `index.html` and upload it to GitHub again. Phones pick up the new version the next time they open the app while online.
