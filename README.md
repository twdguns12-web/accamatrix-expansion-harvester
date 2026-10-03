# AccaMatrix Expansion Harvester

GitHub Actions feed builder for **new leagues that are not already handled by the normal AccaMatrix updater**.

It leaves the existing AccaMatrix Football-Data feeds alone. The job checks out the public-domain `openfootball/europe` repository, converts Football.TXT datasets to JSON with `fbtxt2json`, selects the configured expansion competitions, and publishes a normalised feed for the AccaMatrix WordPress plugin.

## What the weekly job produces

- `data/manifest.json` — league health and per-league feed paths.
- `data/fixtures.json` — future fixtures from leagues currently marked READY.
- `data/leagues/OF_*.json` — up to the latest three available seasons for each selected league.
- `data/STATUS.md` — human-readable league health table.

The WordPress importer only imports leagues whose manifest health is `READY`.

## Selected leagues

The full target list lives in `config/leagues.json`. Poland Ekstraklasa and Romania Liga 1 are deliberately disabled because those competitions are already supplied by the existing AccaMatrix updater.

## GitHub setup

1. Create a new GitHub repository, for example `accamatrix-expansion-data`.
2. Upload the contents of this package to the repository root.
3. Open **Actions** and enable workflows if GitHub asks.
4. Run **Update AccaMatrix expansion feed** manually once.
5. Open `data/STATUS.md` after the run to see which selected OpenFootball leagues are READY, STALE or MISSING.
6. In WordPress go to **AccaMatrix → Expansion Feed** and enter either:
   - the GitHub repository URL, e.g. `https://github.com/YOURNAME/accamatrix-expansion-data`, or
   - the raw manifest URL directly.
7. Use **Test Feed**, then **Sync Expansion Now**.

The scheduled GitHub job runs every Monday at **04:17 UTC**. You can change the cron line in `.github/workflows/update.yml`.

## Data integrity

The harvester never invents data. A competition is marked:

- `READY` — current/recent results or future fixtures were found.
- `STALE` — a matching dataset exists but is too old and contains no upcoming fixtures.
- `MISSING` — no usable matching dataset was found.
- `SKIPPED` — intentionally disabled because AccaMatrix already has that competition from another source.

The WordPress importer uses a distinct `openfootball-github` source marker and does not replace the existing Football-Data league definitions.

## Updating the league selection

Edit `config/leagues.json`. Set `enabled` to `false` to exclude a league, or add another league with:

```json
{
  "code": "OF_EXAMPLE1",
  "country": "Example",
  "league": "Example League",
  "enabled": true,
  "season_type": "season",
  "country_hints": ["example"],
  "aliases": ["example league"]
}
```

The code must be unique.

## Source and licence

The upstream match data is read from `openfootball/europe`, whose datasets are published under CC0 / public-domain terms. This repository contains only the AccaMatrix harvesting/normalisation code plus generated copies of selected upstream records.
