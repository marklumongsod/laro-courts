# Larô Courts

Court rental management for pickleball venues in Metro Manila — a working prototype.

Players find and book court-hours. Front desk staff run the floor. Owners manage branches,
pricing, courts and revenue. One self-contained HTML file, no build step, no backend.

**Live demo:** enable GitHub Pages on this repo (Settings → Pages → Deploy from branch → `main` / root).

---

## Running it

Open `index.html` in a browser. That's it.

To serve it locally instead:

```bash
python -m http.server 8000
# then open http://localhost:8000
```

## Trying it out

There is no login. Click your name in the top-right to switch between accounts —
each one has a different job, and sees only the screens for that job.

| Account | Role | What to look at |
|---|---|---|
| **Mark Lumongsod** | Player | Books courts, has a line-up on two sessions |
| **Rina Santos** | Player | Busiest player — bookings across several venues |
| **Dex Alonzo** | Player | Brand new, nothing booked — shows the empty states |
| **Kaye Medina** | Player | Private profile, seen from the owner's side |
| **Tin Bautista** | Front desk | Floor and Attention only — cannot book, cannot see pricing |
| **Che Ramos** | Venue owner | Two branches, plus Revenue, Pricing, Team and Branches |

The clock is fixed at **Saturday 26 September 2026, 2:21 pm** so the floor always has a
believable mix of completed, in-progress and upcoming bookings.

### Worth clicking

- **Courts → Map** — venue pins with live open-slot counts, road routing, driving distance
- **A venue → the availability board** — pick a slot, add a line-up, reserve
- **Che → Floor** — check in, mark a no-show, take a walk-in, block a court
- **Che → Floor → Complete session** — the booking moves into that player's history
- **Che → Pricing** — change the base rate and watch it apply across the whole app

## How it is built

Single file. Vanilla JavaScript, no framework, no bundler.

- **One booking table.** The venue floor and a player's bookings are the same records read
  two ways, so a status change at the desk is immediately visible to the player.
- **Per-venue roles.** Access is a membership row (`user × venue × role`), not a field on the
  user — someone can be front desk at one venue and a plain player everywhere else.
- **Leaflet** for maps, loaded from CDN. Routes come from a small Metro Manila road graph
  with Dijkstra over it.
- **Procedural court artwork** as inline SVG, plus six photographs from Wikimedia Commons.

Everything lives in memory — a refresh resets it. Your own profile and photo persist via
`localStorage`.

## Things that change outside the prototype sandbox

The prototype was built to run inside a restricted frame. On GitHub Pages those limits are
gone, so two things can be switched on:

1. **Real map tiles.** `baseLayers()` draws a vector base map because raster tiles were
   blocked. Replace its body with:

   ```js
   L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
     { maxZoom: 19, attribution: "&copy; OpenStreetMap contributors" }).addTo(map);
   ```

2. **Google Maps hand-off.** `openMaps()` falls back to a copyable link because the frame
   blocked navigation. Outside it, `window.open` works and the dialog will not appear.

## Photo credits

Court photographs from Wikimedia Commons, used as illustrative stand-ins — they are not
these venues.

| Venue | Photograph | Licence |
|---|---|---|
| Rally Point BGC | *Pickleball hall with Short mat bowls rink* — Lee Vilenski | CC BY-SA 4.0 |
| The Kitchen Makati | *Plankinton Arcade, Milwaukee* — Michael Barera | CC BY-SA 4.0 |
| Skyline Rooftop Courts | *Sports Court, Harmony of the Seas* — Larry D. Moore | CC BY 4.0 |
| Katipunan Community Courts | *Harry B. Anderson Tennis Center* — GA Kevin | CC0 |
| Southside Paddle Club | *KATUSA Friendship Week* — US Army | Public domain |
| New Manila Sports Plaza | *KATUSA Friendship Week* — US Army | Public domain |

## Status

Prototype. Venue names and players are invented; street addresses and coordinates are real
Metro Manila locations. Nothing persists server-side and there is no authentication — the
account switcher is demo scaffolding, not a login.
