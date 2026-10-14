# NAIJA BILLIONAIRE LIFE: GAME SPEC

How to use this file: keep it in your GitHub repo as `GAME_SPEC.md`. Each day, tell the AI builder:
"Read GAME_SPEC.md. Build ONLY Section N today. Then tell me how to run it and what you tested."

---

## BUILDER RULES (apply to every section)

1. Read the whole spec once, then build ONLY the section I name.
2. Do not build features from other sections. If you need something from a later section, add a clearly marked stub and leave it empty.
3. Keep the code small, readable and commented, in separate files that match the structure from Section 1.
4. Never break earlier sections. After each section, re-check that earlier screens still work.
5. When finished, always tell me: how to run it, what you tested, what does not work yet, and what files you changed. Update README.md and CHANGELOG.md.
6. Do not claim any feature works unless you built and tested it.

---

## SECTION 0: GAME BIBLE (shared rules for everything)

**The game.** A fictional Nigerian-inspired life and business tycoon, made for fun. All coins, businesses, properties, net worth and rankings are game data. Never imply real money, real profits or real investment opportunities.

**Tone.** The atmosphere is gritty, busy and unpredictable, like a big city in real life: traffic, power cuts, fuel prices, hustle, lucky breaks and bad days. The chaos comes from everyday life events. Conflict is verbal and comedic: arguments with landlords, mechanics, customers, rivals and market haggling, shown as dialogue choices. There is NO physical violence, crime missions, weapons or drugs in version 1. Arguments must never be hateful toward any tribe, religion, gender or group, and insults stay playful. Do not copy any other game's art, fonts, logo, interface, names or characters. Do not use real brands, real people, or the Forbes name.

**Version 1 scope.** 2D, screen-based, mobile-first (design for a 360x800 portrait phone, scale up to tablet and desktop). The avatar is a customizable character shown on screens and does not walk. The town is an illustrated map where the player taps locations. No 3D, no backend, no real payments, no live ads.

**Look and feel (muted, not flashy).**
- Palette: charcoal #1B1D21, asphalt #2A2D33, concrete #8A8F98, off-white #E8E6E1, money green #4F8A5B, dusty amber #C9A24B, brick red #A5493F, dusty blue #4A6A8A.
- No neon, no bright saturated colours, no rainbow gradients. Dark base, with amber used only for important rewards and highlights, and money green only for coins and gains.
- Flat vector style with a slightly gritty, textured feel. Max two fonts (one bold condensed for headings, one clean sans for body).
- Large touch targets, readable contrast, restrained animation that respects reduced-motion settings.
- Original artwork only, made from SVG or CSS shapes for now so it can be replaced later with generated art.

**Naming.** Currency: Coins. Leaderboard: Naija Rich List. Score: Net worth. Cities: Lagos, Abuja, Port Harcourt (with fictional districts and businesses). IDs use lowercase_with_underscores.

**Data shapes (keep consistent in every section).**
- player: id, username, avatar, coins, xp, level, reputation (0-100), cityId, createdAt, saveVersion
- avatar: skinTone, hairstyle, outfit, accessory
- business: id, name, sector, cityId, level, price, baseIncome
- property: id, name, type, cityId, price, value
- city: id, name, locations[]
- event: id, title, description, choices[]

**Languages.** The game must support many languages from the start. Never hard-code visible text in screens. Every piece of text lives in language files (`js/i18n/en.js`, `pcm.js` for Nigerian Pidgin, `yo.js`, `ha.js`, `ig.js`, and more later) and is read through a `t("key")` function. English is the fallback if a key is missing. Pidgin is a full first-class language, not a joke mode. Pidgin, Yoruba, Hausa and Igbo text should be reviewed by native speakers before launch.

**Tech rules.** Plain HTML, CSS and JavaScript (no framework, no backend). All game numbers live in one config file. Save to localStorage with a saveVersion and a migration step, so old saves never crash when new sections are added. Handle missing or corrupted saves. Label all prototype data as demo data.

---

## SECTION 1: PROJECT SETUP AND SAVE SYSTEM

**Purpose.** The foundation everything else sits on.

**Build.**
- Folder structure: `index.html`, `css/`, `js/` (with `config.js`, `state.js`, `save.js`, `ui.js`, `screens/`), `assets/`, `README.md`, `CHANGELOG.md`.
- A simple screen router (move between screens without page reloads), with a bottom navigation bar: Home, Businesses, Town, Profile, Rich List, More.
- Save system: save/load from localStorage, saveVersion, migration function, safe fallback on corrupted data, and a "reset progress" function.
- Apply the palette and fonts from Section 0 as CSS variables.
- Placeholder screens for every tab.
- Loading, empty and error states.
- Language system from Section 0: a `t("key")` function, English and Nigerian Pidgin files with the navigation labels translated, and a language switcher in More. The chosen language is saved.

**Done when.**
1. The app opens on a phone-width screen and navigation works between all placeholder screens.
1b. Switching between English and Pidgin changes all visible text on the placeholder screens.
2. Saved data survives a refresh.
3. Deleting or corrupting the saved data does not crash the app.
4. All colours come from CSS variables.

---

## SECTION 2: CORE ECONOMY AND HOME DASHBOARD

**Depends on.** Section 1.

**Build.**
- New player starts with a modest number of Coins (set in config).
- Home dashboard showing coins, net worth, income per minute, level and the next goal.
- Income accrues over time (based on saved timestamps) up to a cap of a few hours' worth. The player taps Collect to receive it.
- Formulas in config, with these starting defaults: upgrade cost = basePrice x 1.8^level, income = baseIncome x 1.5^(level-1). Net worth = coins + coins invested in businesses + property values.
- Prevent negative balances. Disable and explain any button the player cannot afford.
- Repeated taps, refreshes or opening multiple tabs must not create free coins.
- A short first-run welcome tutorial (earn, collect, upgrade) that can be replayed later.

**Done when.**
1. Collecting adds the correct number of coins, once.
2. Refreshing or tapping repeatedly does not duplicate coins.
3. The balance can never go below zero.
4. The tutorial works and can be restarted.
5. Known limitation noted: changing the phone clock can cheat income until a server is added.

---

## SECTION 3: BUSINESSES

**Depends on.** Sections 1 and 2.

**Build.**
- Business marketplace screen and business details/upgrade screen.
- Three starter businesses with fictional names, each with a different sector, purchase price, 5 upgrade levels, income and a simple icon or illustration: a street food stand, a phone repair shop, and a small logistics bike service.
- Show the cost and benefit of every purchase clearly, and a progress indicator for each business.
- Make it easy to add more businesses later by editing config only (no code change).

**Done when.**
1. The player can buy and upgrade each business, and the right cost is deducted.
2. A business the player cannot afford cannot be bought.
3. Income and net worth update correctly after each purchase.
4. Adding a fourth business through config only works.

---

## SECTION 4: AVATAR

**Depends on.** Sections 1 and 2.

**Build.**
- A character creator using layered SVG parts: skin tone, hairstyle, outfit and accessory (four slots, 6 to 8 options each, respectful and varied).
- Username input (3 to 16 characters, letters, numbers and underscores, with a placeholder for a future profanity filter).
- Preview, randomise button and save.
- Some outfits and accessories unlock at higher levels or net worth, as set in config.
- The avatar shows on the Home screen and Profile screen.

**Done when.**
1. The player can create and change their avatar, and it persists after a refresh.
2. Locked items are visibly locked and show how to unlock them.
3. An invalid username shows a clear message.
4. The avatar looks consistent with the palette and style in Section 0.

---

## SECTION 5: PROPERTY AND LIFESTYLE

**Depends on.** Sections 1 to 3.

**Build.**
- Property marketplace with a progression from modest rented room to flat, house, mansion and penthouse, each with a fictional name, location (city), price and value.
- A few vehicles and office upgrades as lifestyle items.
- Property value counts toward net worth. Show the cost and benefit of every purchase.
- Locked items show what level or net worth unlocks them.

**Done when.**
1. Properties can be bought only when affordable, and net worth updates correctly.
2. Owned items appear on the player's profile.
3. New properties can be added through config only.

---

## SECTION 6: TOWN MAP

**Depends on.** Sections 1, 3 and 5.

**Build.**
- Illustrated 2D map of Lagos made from SVG, with tappable districts (for example a business district, a market zone, a coastal estate area and a tech quarter, all with fictional names).
- Tapping a district shows what is there: available businesses and properties, and what is locked.
- Abuja and Port Harcourt appear as locked cities that unlock at levels set in config.
- A subtle day/night tint is optional. The look must follow the muted palette.
- Moving to a new city or district updates cityId.

**Done when.**
1. Every tappable area opens and shows the right content.
2. Locked cities show their unlock requirement.
3. The map is usable on a narrow phone screen with touch.

---

## SECTION 7: CHAOS EVENTS

**Depends on.** Sections 1 to 3 (and 5 if available).

**Build.**
- A pool of random life events in config, each with a title, short description and 2 or 3 choices. Each choice has an outcome (coins, xp, reputation, or temporary income change).
- Example events (fictional): a power cut hits your business (pay for generator fuel or lose income for a while), a traffic jam delays deliveries, a fuel price jump, a viral moment that boosts income, a landlord raises rent, a "double your coins" offer that is a scam (ignoring it gives xp, accepting loses coins).
- Trigger rules: random with a cooldown, never more than one at a time, shown when the player is active. No event may push coins below zero.
- Events should be funny and relatable, never mocking any group of people.

**Done when.**
1. Events appear at reasonable intervals and never stack up.
2. Every choice applies the correct outcome and shows it clearly.
3. The balance never goes negative, and refreshing does not re-trigger or skip an event.
4. New events can be added through config only.

---

## SECTION 7B: STREET ARGUMENTS AND HAGGLING

**Depends on.** Section 7.

**Build.**
- Verbal arguments and bargaining scenes, as short dialogue screens with a fictional character (landlord, market trader, bike rider, estate agent, a customer who says you owe them change).
- The player picks a reply each turn (calm, firm, funny, walk away). Replies change an outcome meter such as price, reputation or mood, and the argument ends in a win, a compromise or a loss, each with a coin, xp or reputation result.
- Loud and dramatic, but only words: no violence, no threats, no slurs, no mocking of any tribe, religion, region or group. Characters are fictional.
- Same trigger rules as Section 7: cooldown, one at a time, coins never below zero, new arguments added through config only.

**Done when.**
1. An argument can be started, played and ended, and the outcome applies once.
2. Every branch ends properly and none can get stuck.
3. Refreshing in the middle of an argument does not give a free win or lose coins twice.

---

## SECTION 7C: BANTER

**Depends on.** Sections 7 and 7B.

**Build.**
- Quick banter moments with NPC rivals and friends: a short line is thrown at the player, who picks one of 3 comebacks (witty, humble or savage-but-friendly). Witty replies earn reputation and xp, and a wrong choice just ends in a funny loss with no big penalty.
- Banter lines live in config, in English and Pidgin, and stay playful: no slurs, no attacks on tribe, religion, body, family tragedy or any group.
- A "banter streak" counter shown on the profile, and a shareable banter card (the line and the comeback) with no personal data.
- Banter can be triggered from phone messages (Section 13), contests (8B) and arguments (7B).

**Done when.**
1. A banter moment shows, accepts a reply and applies its result once.
2. The streak updates correctly and survives a refresh.
3. All banter is available in English and Pidgin, and the filter list blocks banned words.

---

## SECTION 8: MISSIONS AND REWARDS

**Depends on.** Sections 1 to 3.

**Build.**
- Daily missions (for example collect income 3 times, upgrade a business, handle an event), a rewards page and a claim button.
- Milestone missions with larger rewards.
- Each reward can only be claimed once. The daily reset is based on the saved date, and cannot be repeated by refreshing.

**Done when.**
1. Completing a mission makes the reward claimable, once.
2. Refreshing does not give duplicate rewards.
3. Missions reset correctly the next day.

---

## SECTION 8B: CONTESTS (single player vs NPC rivals)

**Depends on.** Sections 2, 3, 7C and 8.

**Build.**
- Timed contests (for example "Richest street food stand this week", "Most income collected in 24 hours", "Best haggle score") against fictional NPC rivals with their own avatars and banter.
- Entry rules, a clear goal, a countdown based on saved dates, a live scoreboard against the rivals, and a prize (coins, xp, a badge) at the end. Entry fees, if any, are small Coins and shown clearly. Never real money.
- Rivals' progress is simulated from config, fair and beatable. The result is final once the contest ends and prizes are paid once.
- A contest history on the profile.

**Done when.**
1. A contest can be joined, tracked and finished, and the prize is paid once.
2. Refreshing or changing the phone clock does not pay the prize early or twice.
3. Contest entry cannot take coins below zero.

---

## SECTION 9: PROGRESSION, ACHIEVEMENTS AND PROFILE

**Depends on.** Sections 1 to 8.

**Build.**
- XP, player levels and stages (Newcomer to Established Entrepreneur to Business Mogul), with clear goals for each stage.
- A list of achievements (first business, first upgrade, first property, first event handled, new city unlocked, level milestones) shown on the profile.
- Profile screen with avatar, username, level, net worth, owned businesses and properties, and achievements.

**Done when.**
1. XP and levels update correctly and the next goal is shown.
2. Achievements unlock once, at the right time.
3. The profile shows accurate data.

---

## SECTION 10: NAIJA RICH LIST

**Depends on.** Sections 1 to 9.

**Build.**
- A leaderboard with at least 20 fictional sample players (rank, username, avatar, level, net worth). The real player is inserted at their computed rank.
- Tabs for Daily, Weekly, Monthly, All-time and Friends. Only sample data is shown for now. Each tab says clearly "Sample data. Online rankings come later."
- A shareable achievement card (username, rank, net worth, avatar) that can be saved as an image or shared through the browser share feature.
- Do not let the player edit their rank through the interface.

**Done when.**
1. Rank is computed consistently with net worth.
2. Sample data is clearly labelled as sample data.
3. The share card works on a phone.

---

## SECTION 11: SETTINGS, PRIVACY, HELP AND FUTURE INTEGRATION POINTS

**Depends on.** Sections 1 to 10.

**Build.**
- Settings: sound on/off, reduced motion, restart tutorial, reset progress (with confirmation).
- Privacy information, terms and help pages with clear placeholders for real versions before any public launch.
- A coin shop screen in demo mode only. Buttons show "Demo mode: no real purchases." No fake payment confirmations.
- A clearly labelled placeholder for a future rewarded-ads provider. In this prototype it grants nothing. (Future plan: 5 coins per verified completed rewarded ad, with a configurable daily limit, never for clicks or refreshes.)
- Collect no unnecessary personal data. No deceptive countdowns or fake discounts.

**Done when.**
1. Every setting works and persists.
2. Reset asks for confirmation and fully resets the game.
3. No button implies real money or real purchases.

---

## SECTION 12: FINAL QA AND POLISH

**Depends on.** All earlier sections. Run this after Sections 13 and 16 are built, so it covers the whole offline prototype.

**Do.**
1. Run through the whole game as a new player: tutorial, first business, first upgrade, avatar, first event, mission, property, Rich List.
2. Check that coins, net worth and income stay consistent everywhere.
3. Check that the game works on narrow phone screens and still loads fast.
4. Check that old saved data from earlier sections loads without crashing.
5. Check that every colour follows the Section 0 palette.
6. List every known bug or limitation in README.md.
7. Explain what backend services a real launch would need.

---

## SECTION 13: IN-GAME PHONE (single player)

**Depends on.** Sections 1 to 9. Can be built before any backend.

**Build.**
- A phone screen inside the game (opened from a phone button), with Contacts, Calls, Messages and a call history.
- Fictional NPC contacts (your landlord, a supplier, a mechanic, a friend, an investor). They can call or message the player at times set in config, and the player can call them.
- A call screen that looks like a phone call: caller name, ringing, accept or decline, a timer, mute and hang up. NPC calls are text dialogue with choices (no real audio yet), and can trigger events, missions or arguments from Sections 7, 7B and 8.
- Ringtone and vibration only if sound is on and reduced-motion is respected.
- Missed calls are saved and shown.

**Done when.**
1. An NPC call rings, can be accepted or declined, and the outcome applies once.
2. Missed calls appear in history after a refresh.
3. The phone works with sound off and on a narrow screen.

---

## SECTION 14: PLAYER ACCOUNTS AND FRIENDS (needs a backend)

**Depends on.** Sections 1 to 13 and a hosted backend (for example Supabase or Firebase).

**Build.**
- Sign-up and login, a unique username, and a profile that other players can see.
- Add friend by username, accept or decline requests, friends list, block list.
- Online or offline status that the player can turn off.
- Privacy: collect only what is needed, and give a way to delete the account.

**Done when.**
1. Two real accounts on two phones can become friends.
2. Blocking someone hides you from them completely.
3. No one can read or change another player's data by editing requests (use the backend's security rules).

---

## SECTION 15: PLAYER-TO-PLAYER CALLS AND MESSAGES (needs Section 14)

**Depends on.** Sections 13 and 14.

**Build.**
- Text messages between friends, with a message limit per minute.
- In-game voice calls between two friends, using the same phone screen as Section 13: call a friend, they get a ringing screen, accept or decline, timer, mute, hang up. Use WebRTC for the audio, with a signalling service on the backend.
- Safety first: calls only between accepted friends, the player can mute, block and report from the call screen, microphone permission is asked clearly, there is no recording, and "Do not disturb" is available.
- Handle failures: no internet, microphone denied, the other person offline or busy.
- Group calls, video and calls with strangers are NOT included.

**Done when.**
1. A friend can be called and can answer, and both hear each other.
2. Decline, busy, offline and permission-denied cases each show a clear message.
3. Block and report work during a call.
4. A blocked person can never call the player.

Note for Danny: voice calls need a TURN server on some mobile networks, which costs money at scale, and Bolt.new or Lovable cannot host the backend alone. Build this only after Sections 1 to 13 work.

---

## SECTION 16: LANGUAGES (build after Section 13, before Sections 14 and 15)

**Depends on.** Sections 1 to 13.

**Build.**
- Move any text still hard-coded into the language files from Section 0.
- Complete Nigerian Pidgin for every screen, event, argument (7B) and phone message (13). Add Yoruba, Hausa and Igbo for the main screens first, with English fallback for the rest.
- A language picker on first launch and in settings. Layout must not break with longer text.
- A `LANGUAGES.md` file listing each language, how complete it is, and which parts still need review by a native speaker.
- Use natural everyday wording, not word-for-word translation.
- When chat arrives in Section 15, messages between players are shown as typed in any language and are never auto-translated or filtered by language.

**Done when.**
1. Switching language changes every visible text, and the choice is saved.
2. Missing translations fall back to English, never to blank text or code keys.
3. No screen has text that overflows on a 360px wide phone.

---

## SECTION 17: PLAYER CONTESTS AND BANTER (needs Section 14)

**Depends on.** Sections 8B, 7C, 14 and 15, plus a backend.

**Build.**
- Friend challenges and open contests between real players, built on the Section 8B contest rules: friends invite each other, both accept, a timer runs, and the winner is decided by the backend.
- All scoring, timing and prizes are calculated on the server, never on the phone, so no one can cheat by editing their game or clock.
- Banter between friends using preset lines and emoji from the banter library, with free-text banter only inside friends chat (Section 15), with report and block.
- Fair play: no entry fees in real money, prizes are only Coins and badges, and clear contest rules and a dispute button.

**Done when.**
1. Two real players can start, play and finish a contest, and the same winner shows on both phones.
2. Editing local data or the phone clock does not change the result.
3. Blocked players cannot be challenged or banter with you.

---

## NOT IN VERSION 1 (do not build yet)

Sections 14, 15 and 17 (accounts, friends, player calls, messages, real-player contests), a real shared leaderboard, server-side coin validation, real payments, live rewarded ads, a walking avatar and open world, 3D graphics, friend challenges, and more cities and sectors.

Real launch needs a backend for accounts, secure validation of coins and purchases, and a real leaderboard. This is the main step after the prototype is finished.
