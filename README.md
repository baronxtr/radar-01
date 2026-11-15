# Token Radar — pre-token protocols ranked by implied value

A radar for the protocols whose token does not exist yet. It reads a sealed DefiLlama
snapshot (`/input/snapshot.json`, as of 2026-11-14), scores every rankable pre-token protocol
with one model, and writes a ranked board, a dashboard and a dossier.

## What it does

- **Covers the whole radar, not just the floor.** The snapshot's watchlist is 40 protocols
  with at least $50M of TVL and no token on record. This board ranks all 198 rankable
  pre-token protocols in the snapshot: the 40 on the watchlist plus every rankable pre-token
  protocol the watchlist leaves out. Wrapped, staked and bridged products of a token that already
  trades are set aside too, as are centralised exchanges and versioned pools. The protocols whose
  token already trades are shown separately with their market caps.
- **A model that follows from the snapshot's own figures (radar-2).** Implied value is
  `TVL x 0.30`, times a growth multiplier from the seven-day TVL change (clamped to
  -80%..+150%, weight 0.6), times a maturity multiplier from how long the protocol has run
  with no token (0.50 at listing, up to 1.00 after four years), times a reach multiplier
  from how many chains it is live on (up to 1.20). No protocol name is special-cased.
- **A farm plan for every row.** Eight steps per protocol, derived from its category,
  chains, TVL and age: what to do, what it costs and how long it takes. Capital scales with
  TVL ($50–$500), gas follows the chain, and the hold window follows the protocol's age.
  The plan covers all 53 pre-token categories in the snapshot, including the small ones
  (Anchor BTC, Decentralised BTC, CeDeFi, OTC, card issuers), and ends with a monthly
  re-check: close the farm if a token has launched or the vault has shut.
- **Risks to weigh, per row.** Each row carries up to three short flags drawn from its own
  record: a listing under 90 days old, a seven-day TVL move past 20% down or 100% up, a
  single chain, TVL under $10M, or a missing listing date or category. They are reasons to
  look closer, not verdicts.
- **A dashboard a reader can use.** Sortable board, search, category and chain filters, a
  watchlist-only toggle, a sortable live-token tab, and a detail view per protocol with the
  model's full breakdown, its risk flags and its farm plan. A stats bar above the board
  totals the TVL, the median implied value, the top ten's share and the largest categories.
  Static, no network, no framework.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json|directory>
```

Node 22 or later, standard library only. The run is deterministic: the same snapshot
bytes always write the same output bytes. `out/` holds `board.json`, `data.js`,
`dashboard/`, `screenshots/` and `dossier.json`.

## Layout

- `main.ts` — entrypoint: reads the snapshot, writes the board, the data, the dashboard,
  the screenshots and the dossier.
- `lib/model.ts` — the radar-2 model, the farm plans and the snapshot parsing.
- `vendor/makefun-board.ts` — make.fun's dossier library (vendored from the kit).
- `dashboard/` — `index.html`, `app.css`, `app.js`: the static page.
- `screenshots/` — the board, a protocol's detail view and a filtered view.

Implied value is this radar's own model of what a token would be worth if the protocol's
record stayed as it is. It is not a prediction that a token will be issued, and not a price.
