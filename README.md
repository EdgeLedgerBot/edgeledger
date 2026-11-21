# EdgeLedger

The all-sports board. ARCHITECTURE (decided by Carlos, 2026-11-20): the
live site is the EdgeLedger folder in the Muse space on OpenCloud. Muse
edits the HTML there; the Ace server serves that folder as the website
(a small publish sync mirrors the folder into the web root, because
OpenCloud storage is not directly servable); Cloudflare fronts it at
edgeledger.acevedo.app. Hermes writes each sport's JSON snapshot into
the same folder's data/ directory, and the page reads it same-origin.

A GitHub mirror may be added later as the public archive and track-record
history; it is not part of the live path. The page's snapshot source list
tries the same-origin data/ file first, then the GitHub raw and jsDelivr
URLs as fallbacks.

Privacy rule baked into the schema: picks, prices, and percentages only.
No stake amounts, no contract counts, no bankroll, no dollar P&L — anywhere.

## Layout

    index.html                  the MLB board (calculator + panels)
    data/mlb-snapshot.json      the MLB snapshot Hermes maintains
    data/<sport>-snapshot.json  one file per sport as each lane restarts

Carlos's sports: MLB (live now), NFL, NBA, NHL, college football (ncaaf),
and college basketball (ncaamb). Each gets its own board page on this same
template when its lane is live again. MLB is first because it is the lane
currently running; the rest are on standby until Carlos restarts them.

## Setup

Carlos's part: send Hermes the two asks (server setup + snapshot) in
~/workspace/kalshi-ev/. Hermes's part: the web root and publish sync on
Ace, the proxied Cloudflare record for edgeledger.acevedo.app, and the
snapshot writer dropping data/mlb-snapshot.json into this folder on its
cadence. Muse's part: the files themselves, edited here in OpenCloud.

How the data flows: Hermes overwrites the snapshot in place; the publish
sync carries it to the web root; the page fetches it same-origin at load
and re-checks every 5 minutes. An edit to index.html in this folder is
the website updating, one sync later.

## Snapshot schema (what Hermes writes, per sport)

See HERMES-SNAPSHOT-ASK.md in ~/workspace/kalshi-ev/ for the full ask.
Sections: meta (generated_at, config_sha256, kill_switch, bot_version),
board (one row per evaluated game: game, start_time, kalshi_ticker, side,
model_probability as a decimal, kalshi_ask_cents, spread_cents, tier,
edge_points and required_edge_points as decimals, fee_estimate_cents,
verdict trade/no_trade, blocking_gate or null), positions (kalshi_ticker,
game, side, entry_price_cents, opened_at, current_price_cents), record
(settled_count, wins, losses, roi_pct as a decimal, clv_avg_points as a
decimal — null until reconciled, never zero-filled).

## Later, if it earns it

If the collected data outgrows static snapshots, the Ace server can grow
this into a dynamic site over the same files and schema. Nothing here
has to be thrown away to make that move.
