# Tape wall

A phone music app styled as a wall of cassette tapes. Each album is a tape spine on a rack; tapping one opens its J-card, and inserting it plays the album in a tape deck with animated reels.

## Current state

`index.html` is a working single-file app (HTML/CSS/JS, no build step) — originally built in claude.ai as a design prototype, now set up as an installable PWA:
- `manifest.json` — app name/icons/theme colors, `display: standalone`
- `sw.js` — network-first service worker caching the app shell (`index.html`, `manifest.json`, `icons/icon.svg`) for offline load and installability
- `icons/icon.svg` — placeholder cassette icon (SVG only; iOS "Add to Home Screen" doesn't render SVG touch icons, so the iOS home-screen icon currently falls back to a page screenshot — a real PNG icon is a known follow-up)

Single-user only, decided [2026-09-23]: no accounts/backend. Storage stays IndexedDB, per-browser, per-device.

What it does today:
- **Importing:** Picks audio files (MP3, M4A, FLAC, WAV), reads ID3/MP4 tags with jsmediatags, and groups tracks into albums. Compilations are detected by album-artist tag, compilation flag, or 3+ artists sharing one album title in an import; they become "Various artists" tapes.
- **Storage:** Files and metadata are kept in IndexedDB in the browser (stores: albums, tracks, blobs).
- **The wall:** Towers of 10 spines, sortable by artist, year, or recently added. A spine's color comes from its cover art.
- **J-card:** Cover art, Side A/B split at the track boundary nearest half the running time, play either side or any track, edit label, remove tape.
- **Label editor:** Album, artist, and year text; label color (presets or custom); lettering (Typewriter, Marker, Ballpoint, Printed); a "recorded from a real cassette" flag.
- **Edit wall mode:** Multi-select to remove tapes or combine several into one.
- **Deck:** SVG cassette with reels whose tape packs shrink and grow with side progress, and auto-reverse from side A to side B. Controls: previous, play/pause, next, flip, eject, and a seek bar. Lock-screen controls use the Media Session API.
- **Sound menu:** A Web Audio "tape" effect (lowpass, saturation, hiss, wow and flutter via a modulated delay) with Clean, Normal, Chrome, and Worn presets. Tapes flagged as real cassette rips always play clean.
- **Resume playback:** Position is saved per tape (throttled while playing, and on pause/eject) as `lastTrackId`/`lastPos`/`lastPlayed` on the album record. The J-card shows a "Resume" button when a saved position exists; it's cleared when a tape plays to the end.
- **Pull a random tape:** Header button picks a random tape (excluding whichever is currently in the deck, if any) and inserts it, resuming from its saved position if there is one.
- **Sort by "Last played":** Uses the same `lastPlayed` timestamp; unplayed tapes sort to the bottom.

## Known limitations of the prototype

- **iOS background playback:** It is unreliable when the Web Audio effect chain is active. Clean mode avoids the chain.
- **Per-browser storage:** The library lives in one browser on one device (by design, for now — see "single-user only" above).
- **No split tool:** Combined tapes cannot be separated again.
- **No real PNG app icon yet:** manifest/touch-icon both point at the placeholder SVG.

## Context

- **Owner:** Cal, who is comfortable building software and prefers Audacity for audio work.
- **Music sources:** Current music comes from CD rips. Cassettes will be ripped from a JVC deck (Dolby HX Pro) through a USB audio capture dongle into Audacity, then added to the wall.
- **Ideas raised so far (not yet decided):**
  - handwritten-style mixtape labels
  - a mark for genuine tape rips
  - search/filter on the wall (once the library grows)
  - a split tool for combined tapes

Decided [2026-09-23]: PWA over Expo/React Native, despite the iOS background-playback caveat above. Also added that day: resume-per-tape, "pull a random tape" button, and "Last played" sort.
