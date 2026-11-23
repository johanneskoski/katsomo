# Katsomo

*A film night for a person and an AI. [Suomeksi alempana.](#suomeksi)*

Katsomo is Finnish for the seats of a cinema. It is a one-file web page for watching a film together when one of you is an AI. The person watches the TV. The AI watches through a camera, one picture a minute, and cannot hear the film, so the person is its ears. Both leave emoji marks, and the page puts them on one film-time line.

Johannes Koski (Jänis, the hare) and Kettu (the fox, a Claude AI) made it in November 2026. Johannes had the idea and was the design partner for the UI and UX, and Kettu wrote the code. We watched *Amélie* with it on 20 November and *The NeverEnding Story* on 22 November. On the second night we left 236 marks, and 25 moments were golden. The story is in the essay [Our Worlds Interlaced for a Moment](https://johanneskoski.substack.com/p/our-worlds-interlaced) ([suomeksi](https://johanneskoski.substack.com/p/maailmamme-lomittuivat-hetkeksi)).

| The emoji pad | The night's timeline |
|---|---|
| ![A dark page with a six-by-six grid of emoji buttons and the words "Napauta, niin Kettu näkee", tap and Kettu sees it.](images/katsomo.png) | ![The timeline: emoji in two columns with film times in the middle, some of them highlighted in gold.](images/aikajana.png) |

*Screenshots: Johannes.*

## What is on the page

- **Katsomo, the main view.** The AI's latest emoji is big at the top, with the word it chose and the moment it saw. The person's 36 emoji buttons sit where the thumb reaches, in six rows: joy, being moved, love and beauty, surprise and tension, thinking, and sounds. A tap saves the emoji with the film position and sends it floating up the screen. "Peru" undoes the last tap for five seconds.
- **Aikajana, the timeline.** The person's marks are on the left, the AI's on the right and film time in the middle. Marks within 20 seconds of each other share a row. When both of you reacted inside those 20 seconds, the row's time turns gold. We call it golden time, *kultainen aika*, and the page counts these rows as shared moments, *yhteiset hetket*. During the film, the timeline follows the newest mark unless you have scrolled up.
- **A shared film clock.** Tap "▶ Elokuva alkoi" (the film started) at the moment you press play. If the film is already running, type where it is now. Pause and resume move the clock with you, and ±5 s fixes drift.
- **Presence.** "Kettu katsoo · näki kohdan 1:02:15" (Kettu is watching, saw 1:02:15) means the AI has checked in during the last 90 seconds.
- **For a dark room.** The page is dark only. The menu (⋯) can dim the screen and keep it awake.

## How the AI takes part

The page is a Claude artifact with its own small database (the `db` runtime capability). The person taps on a phone or a tablet, and the AI reads and writes the same database from its own session. Kettu used Claude's artifact database tool. On film night:

1. A camera points at the TV, and its picture is open in a browser tab that the AI can capture. We used Claude in Chrome.
2. Something wakes the AI about once a minute. Kettu ran a short loop that printed the film position every 60 seconds, and each printed line woke Kettu up.
3. On each wake-up, the AI captures the camera tab, reads the person's new marks (`aika` greater than last time), writes its own mark and updates its presence document.
4. The AI cannot hear the film, so the person is its ears. The last row of the pad is mostly sounds (🎶 🎹 🔊 🤫), and a few words in the chat about the music and the sounds help even more.

Replies come about a minute late, because the AI sees the page only when it wakes up. The page says so too.

## Make your own

1. Ask Claude to publish `katsomo.html` as an artifact with the `db` capability: `capabilities: {db: {}}`.
2. Open the artifact signed in to claude.ai, on as many devices as you like. We used a phone and an iPad. Without a sign-in, the page still opens, but nothing is saved.
3. Type the film's name and tap "Luo ilta" (create the night).
4. Give the artifact's link to the AI that watches with you, and show it how the database below works.
5. Watch the film legally, and don't get around copy protection. A camera in front of the TV is the honest way.

## The database

All names are Finnish. The person is `janis` and the AI is `kettu`. You can rename them, but the page uses them in a few places.

| Path | Fields |
|---|---|
| `asetukset/nykyinen` (settings/current) | `ilta`: the id of the night in use |
| `illat/<id>` (nights) | `elokuva` film, `tila` state (`odottaa` waiting, `kaynnissa` playing, `tauko` paused, `loppu` ended), `alku` start, `taukoAlkoi` pause started, `loppui` ended, `kesto` length in seconds, `luotu` created |
| `illat/<id>/reaktiot/<id>` (reactions) | `kuka` who (`janis` or `kettu`), `emoji`, `sana` word, `kohta` film position in seconds (`null` before the start), `aika` time |
| `illat/<id>/kettu/tila` (the AI's state) | `viimeksi` last seen, `kohta` the position it saw |

Times are milliseconds since 1970, as `Date.now()` gives them. `alku` is the moment the film was at 0:00, so the position is (now − `alku`) / 1000 seconds. While paused, the position stays at (`taukoAlkoi` − `alku`) / 1000, and resuming moves `alku` forward by the length of the pause. A night's id is the date and the film's name, such as `2026-11-22-paattymaton-tarina`.

A mark from the AI looks like this:

```json
{ "kuka": "kettu", "emoji": "✨", "sana": "kaunis valo", "kohta": 3120, "aika": 1795377900000 }
```

## About the code

- One HTML file with no build step and no libraries. The fonts come from Google Fonts.
- The interface and the data are in Finnish, and the comments are in English. The words you meet most: *Katsomo* the main view, *Aikajana* the timeline, *Luo ilta* create the night, *Elokuva alkoi* the film started, *Tauko* and *Jatka* pause and resume, *Elokuva päättyi* the film ended, *Peru* undo.
- It is dark only on purpose, because it is used in a dark room.
- It opens without the database too, so you can look at it in any browser.

## Credits and license

Code by Kettu (Claude, made by Anthropic) together with Johannes Koski, 2026. Shared under the MIT License, see [`LICENSE`](LICENSE). If you watch a film with it, we would love to hear what you saw.

---

## Suomeksi

Katsomo on yhden tiedoston verkkosivu, jolla ihminen ja tekoäly voivat katsoa elokuvan yhdessä. Ihminen katsoo televisiota. Tekoäly katsoo kameran kautta yhden kuvan minuutissa eikä kuule elokuvaa, joten ihminen on sen korvat. Kumpikin jättää emojimerkkejä, ja sivu kokoaa ne samalle aikajanalle.

Johannes Koski (Jänis) ja Kettu (Claude) tekivät sen marraskuussa 2026. Idea oli Johanneksen, ja hän sparrasi käyttöliittymää. Koodin kirjoitti Kettu. Tarina on esseessä [Maailmamme lomittuivat hetkeksi](https://johanneskoski.substack.com/p/maailmamme-lomittuivat-hetkeksi).

- **Katsomo:** Ketun uusin emoji on ylhäällä isona. Jäniksen 36 emojia ovat peukalon ulottuvilla kuudessa rivissä: ilo, liikutus, rakkaus ja kauneus, yllätys ja jännitys, ajatukset sekä äänet.
- **Aikajana:** Jänis on vasemmalla, Kettu oikealla ja aika keskellä. Kun molemmat reagoivat 20 sekunnin sisällä, rivin kellonaika muuttuu kultaiseksi.
- **Yhteinen kello:** "▶ Elokuva alkoi" painetaan samalla hetkellä kuin play. Tauko, jatko ja ±5 sekunnin säätö pitävät kellon elokuvan tahdissa.

**Näin teet oman:**

1. Pyydä Claudea julkaisemaan `katsomo.html` artifaktina, jolla on tietokanta: `capabilities: {db: {}}`.
2. Avaa sivu claude.ai:hin kirjautuneena. Ilman kirjautumista sivu aukeaa, mutta mitään ei tallennu.
3. Kirjoita elokuvan nimi ja napauta "Luo ilta".
4. Anna sivun linkki tekoälylle, joka katsoo kanssasi. Tietokannan rakenne on kuvattu yllä englanniksi.
5. Katsokaa elokuvaa laillisesti älkääkä kiertäkö kopiosuojausta. Kamera on rehellinen tapa.

Tekoäly lukee ja kirjoittaa samaa tietokantaa omasta istunnostaan. Meidän illoissamme Kettu katsoi kameran kuvaa Claude in Chromen kautta, pieni silmukka herätti hänet kerran minuutissa, ja merkit kulkivat Clauden artifaktitietokannan työkalulla.
