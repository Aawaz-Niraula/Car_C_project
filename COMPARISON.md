# Nachtwerk (Claude) vs GT3 RS study (Neon): side-by-side

Measured 23 Nov 2026 on the owner's MacBook Pro: headless Chrome (desktop 1512×930 @2x, phone 390×844 @3x) and the owner's real Safari 26.3 via safaridriver. Both sites got identical scripted input.

Neon's project (`~/Downloads/Car_gemini`) was only read and served (read-only, on 127.0.0.1:8081). Nothing in it was changed; its file timestamps are unchanged (index.html and app.js 01:01, styles.css 01:03). Instrumentation (frame timers, a draw-call counter) ran only inside the test browsers.

## Measurements

| | Nachtwerk (Claude) | GT3 RS study (Neon) |
|---|---|---|
| Film frames | 360, every native frame | 348, every native frame (14.5 s) |
| Frame size, desktop / phone | 1440×810 / 576×720 (4:5 reframe that follows the car) | 1280×720 / 768×432 (16:9, cropped by the phone) |
| Frame payload, desktop / phone | 11 MB / 5.6 MB | 10.5 MB / 5.2 MB |
| Film scroll length, desktop / phone | 533vh / 453vh | 520vh / 440vh |
| First film frame on screen (Chrome) | 228 to 237 ms | 224 to 238 ms |
| Chrome wheel scroll, frames over 20 ms | desktop 0, phone 0 | desktop 0, phone 1 |
| Distinct frames shown, same Chrome scroll | 186 | 160 |
| **Safari, draw cost per frame (avg / max)** | **0.80 / 3 ms** | **0.02 / 1 ms** |
| **Safari, frames over 25 ms in a 5 s scroll** | **1 (30 ms)** | **0** |
| Distinct frames shown, same Safari scroll | 302 | 243 |
| Console errors | 0 | 0 |
| Horizontal overflow on phone | none | none |
| impeccable detector findings | 3: 2 cramped padding, 1 hazard stripe (intentional) | 11: 4 low contrast, 6 cramped padding, 1 italic serif display |
| Form fields with no visible label | 0 | 2 (labels are screen-reader only) |
| Links and buttons under 44 px | 12 (mostly the lane's bay buttons) | 8 to 9 |

## What each site is

**Nachtwerk:** an invented marque. Its world is the depot from the footage: a painted lane that works as timeline and scrollbar, numbered bays, enamel sign plates, and the name painted on the wet floor. Extras: sound that follows your scroll, a thread-colour configurator that recolours the real frame, a stills reel, an inspection board, and an allocation form that opens the mail app.

**GT3 RS study:** uses the Porsche name and real model naming. Restrained editorial look: serif display type, mono data, text over the film, and a scrub bar with play and pause plus keyboard control. Its interactive airflow diagram (aero modes and hotspots) is the strongest single interactive piece in either site. Also: a contact sheet of stills, and a finish studio with a form that saves locally.

## Defects found

**Nachtwerk (Claude)**
1. **Safari uses a slow decode path.** It decodes frames with `<img>` instead of `createImageBitmap`. I added this to fix a cropped-frame bug that turned out to be a stale cache. It costs about 40× more per draw in Safari and is the most likely source of the remaining Safari jank. The fix is one line: use `createImageBitmap` everywhere, as Neon does. Not applied, because this is an analysis.
2. The lane's bay buttons are 28 px tall, under the 44 px minimum touch target. On phones they're inactive, but they're still counted.
3. Specification figures are invented. The owner chose to keep them, and they're labelled "Concept figures".
4. The footer credit still reads "[Your name]".

**GT3 RS study (Neon)**
1. **The play and pause icons both show at all times.** `svg { display:block }` overrides the `hidden` attribute (styles.css line 70).
2. **It uses the Porsche name, the 911 GT3 RS model and real product claims** (PTV Plus, Cup 2 R, Weissach, 860 kg downforce). That's fine privately, but it's a trademark risk on a public or paid portfolio.
3. **On phones, the 16:9 frames are cropped to portrait.** In the side shots most of the car is off screen, and the 432 px frames are upscaled about 6× on a 3× phone, so they look soft.
4. The two form inputs have labels only for screen readers (anti-slop R-39 asks for visible labels). The "Save build locally" form keeps the name and email in localStorage.
5. The detector flags 4 low-contrast text spots.

## Verdict

- **Smoothness:** tied in Chrome, desktop and phone. In Safari, Neon is smoother today because of Nachtwerk's defect 1, which is a one-line fix.
- **Design:** Neon is the polished category standard: quieter, closer to what clients expect, with the best single interactive (airflow). Nachtwerk is the more distinctive concept with its own world, and the stronger phone framing.
- **Publishing:** Nachtwerk can be published as it is. Neon's needs rebranding before it goes on a public portfolio.
- **If you can only keep one for a public portfolio:** Nachtwerk, after the Safari one-line fix. If the GT3 RS study is rebranded and its phone framing fixed, it becomes a strong second piece showing a different direction from the same footage.
