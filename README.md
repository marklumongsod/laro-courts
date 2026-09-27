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

## Realtime

Open the page in two windows. Check a player in at the desk in one and the player's own
window updates without a refresh; book a court in one and the slot closes in the other.
The pill beside the logo counts the windows that are connected.

There is no server here — GitHub Pages only serves files — so the transport between
clients is [`BroadcastChannel`](https://developer.mozilla.org/en-US/docs/Web/API/BroadcastChannel).
Each tab is a client, and a change is published to the others the moment it commits.
**This works between windows of the same browser on one machine, not between devices.**
Crossing devices needs a server holding the connections, which is the one thing static
hosting cannot do; with a backend the same code takes a WebSocket instead — the publish
and adopt steps are already separated from the rendering.

A few details that matter if you poke at it:

- **One window owns the invented traffic.** The demo books a court for a made-up player
  every nine seconds. The clients elect a leader so three open windows do not each
  generate their own; if the leader closes, another takes over within a few seconds.
- **Nothing is persisted.** Close every window and the demo returns to its seed, which is
  how the prototype behaved before. A window opened later asks the others for the current
  state and catches up.
- **Browsers without `BroadcastChannel`** still run the prototype; they just run alone.

## Maps

Tiles come from OpenStreetMap. Driving routes come from [OSRM](https://project-osrm.org/),
which routes over the full OpenStreetMap road network, so the line on the map follows real
streets and respects one-way restrictions. It is not traffic-aware, and the estimated drive
time is our own figure — a flat 18 km/h, which is closer to Metro Manila than OSRM's
free-flow speeds.

Behind it is a Dijkstra search over a small hand-built graph of Metro Manila arterials.
That is what paints the moment a venue opens, so the map is never blank while the request
is in flight, and it is what stays on screen if OSRM is unreachable. On its own it is only
an approximation — it has no nodes inside a street grid like BGC's, so short trips come out
as straight lines between the nearest main roads.

Routes are requested once per venue and cached. The public OSRM demo server has no uptime
guarantee and is not meant to carry production traffic; a real deployment would self-host it
or use a paid directions API.

Under a dark theme the tiles are inverted in CSS so the map does not glare against the rest
of the interface.

The route is drawn as a pale casing under a coloured line, the way a navigation app draws
one — the edge is what keeps it legible over the base map. Both colours are theme tokens
(`--route`, `--route-case`).

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
