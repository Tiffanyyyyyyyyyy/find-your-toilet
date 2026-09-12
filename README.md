# find-your-toilet

# Find Your Toilet — in London

A tool that helps you find the nearest public toilet in central London.

## How it works

- Pick an area from the dropdown (or try "Use my current location") and the tool sorts a curated list of ~27 known public toilets by distance, showing a compass arrow, walking time, and a cleanliness rating for each.
- Tap any toilet's row to open real walking directions in Google Maps — the destination is filled in, but the origin is left blank so Maps uses your device's actual current location rather than whatever area you picked here.
- Cleanliness ratings are crowd-sourced: anyone using the tool can leave a 1–5 star rating, and the running average + count is shown per toilet.

## Data sources & accuracy

Each toilet entry is labeled **verified** or **unverified** in its subtitle:

- **Verified** entries were individually checked this session against [Toilet Map](https://www.toiletmap.org.uk/dataset) (the UK's crowd-sourced public toilet database) or an official council open-data file, and their coordinates, fees, and status came from those sources directly.
- **Unverified** entries (mainline stations, Royal Parks toilets, a few landmarks) are general knowledge — real, well-documented facilities, but not individually cross-checked against a primary source in this pass. Treat them as a reasonable starting point, not a guarantee.

This is not a live feed. Toilets close, get refurbished, and change fees or hours — the Toilet Map itself lists things like temporary closures for exactly this reason. If you spot something wrong, the fix is to edit `TOILETS` directly in the HTML file (see below).

### Attribution

Where data was verified against **Toilet Map**, it is used under their [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) licence, which requires attribution:

> Contains data from the Toilet Map © Public Convenience Ltd — CC BY 4.0 (Creative Commons Attribution 4.0 International)

Where data was verified against **Camden Council's open data** (Camden Market, Hampstead Heath, Kenwood House, 5 Pancras Square), that's published under the UK [Open Government Licence](https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/).

If you extend this project with more council or Toilet Map data, keep the attribution intact and note it here.

## Editing the toilet list

Everything lives in two small arrays near the top of the `<script>` block in `london-loo-finder.html`:

```js
const AREAS = [
  { name: "Trafalgar Square", lat: 51.5080, lon: -0.1281 },
  // ...
];

const TOILETS = [
  { id: "trafalgar-square", name: "Trafalgar Square (south side)", type: "Council-run · 20p · verified", lat: 51.508114, lon: -0.128467 },
  // ...
];
```

To add a toilet: append an object with a unique `id` (used as the storage key for its rating — don't reuse or reassign an existing `id`, or you'll merge its rating history with a different location), a display `name`, a `type` string (shown as the subtitle — include fee/access info and whether you've verified it), and its `lat`/`lon`.

To add an area: append `{ name, lat, lon }` to `AREAS`. It'll appear in the dropdown automatically.

## Ratings & storage

Cleanliness ratings use the Claude Artifacts persistent storage API (`window.storage`), scoped as **shared** — meaning every rating is visible to, and averaged across, everyone who opens this artifact. There's no login, so:

- One rating per toilet is enforced only within a single browser session (stars lock after you rate, but reloading the page resets that).
- There's no protection against someone rating the same toilet many times across separate visits.
- Treat the ratings as informal crowd impressions, not an audited score.

If you deploy this outside Claude.ai (e.g. as a static site), `window.storage` won't exist — you'd need to swap in your own backend (Firebase, Supabase, a small API) to persist ratings, or strip the rating feature out entirely.

## Known limitations

- **Coverage is central London only.** The area picker has ~21 hubs and the toilet list has ~27 entries — nowhere near exhaustive. Outer boroughs aren't represented.
- **No live data.** Fees, hours, and open/closed status can drift from what's shown.
- **Directions link always opens Google Maps.** On iOS, some people prefer Apple Maps — swapping in a platform-detected link (`maps.apple.com` vs Google) is a reasonable follow-up if that matters to you.
- **Geolocation depends on where the file is opened.** Works in a normal browser tab; blocked inside Claude.ai's in-chat preview sandbox.
