DrillWizard-packs

Rate packs for Drill Wizard. This repo exists for one reason: to serve latest.json over HTTPS so the app can refresh its rate tables without an App Store release.

It must stay public. GitHub Pages will not serve a private repo, and the app fetches from a different origin than it runs on.

What the app does

Drill Wizard fetches:

https://ephemerislabs.github.io/DrillWizard-packs/latest.json

from Settings → Check for updates. Accepted packs are cached in the member's own storage and survive backups, the native mirror, and boot-restore. The built-in pack stays as a floor — a failed or rejected fetch never leaves the app without rates.

The file format

latest.json may be a bare array, a single pack object, or an object with a packs array. This repo uses the last form so the file can carry human-readable metadata:

json
{
  "schema_version": 3,
  "generated": "2026-07-30",
  "packs": [ { "pack_id": "2026", ... } ]
}
What makes a pack valid

Only three things are required:

Key	Why
pack_id	identity, and the label in the toast
basic_pay	a pack without it is not worth installing
effective.military_pay_start	how packs sort and how the app picks one for a date

Everything else is optional. A pack that omits bas, sgli, tsp_funds, brs_match, tsp_limits or any other table is accepted, and the app backfills the missing tables from its built-in pack (PACK_FILL). That is deliberate: a January refresh should be able to ship only the tables that actually changed.

Do not ship a pack that omits basic_pay to "inherit" it — it will be rejected and the toast will say which key was missing.

Publishing a new pack
Add the new pack object to the packs array (keep the old ones — the app picks by date).
Set its generated to a string later than the one it replaces. Packs with the same pack_id only replace an existing one when generated is greater.
Set effective.military_pay_start to the date the new tables take effect.
Commit. The app picks it up on the next Check for updates.
Rate calendar
Table	Rolls
Basic pay, BAS, BAH RC/T	1 January
VA compensation	1 December
CONUS per diem (GSA)	1 October
OCONUS per diem (DTMO)	monthly
TSP limits, FICA wage base	1 January

The app's Rates → Rate freshness panel flags any table whose edition has rolled past its capture date, so a stale pack becomes visible rather than silent.

What this repo cannot fix

Locality BAH, the ZIP→MHA map, CONUS and OCONUS per diem, and the state-tax table are compiled into the binary and still require an App Store release. The freshness panel says so on the card.

© 2026 Ephemeris Labs LLC
