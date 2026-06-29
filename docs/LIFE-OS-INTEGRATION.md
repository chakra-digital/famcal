# FamCal → life-os integration plan (review draft)

**Status:** PLAN ONLY. Nothing here is executed. No writes to `life-os` or `garage-hq` yet.
**Decision locked:** Custody = **Option A (4 nights / 2 nights)**, anchor Sat 2026-06-27, Dad = primary home.

## Doctrine fit (why life-os, not a new vehicle)
- `garage-hq` = map/registry, no cargo. `vehicle-template` = clone source. Vehicles = walled-off repos w/ own machinery. `tlwj-os` = sensitive vault.
- `life-os` = `personal_life_ops`. Co-parenting **is** personal life ops → it's a **domain inside life-os**, not a peer vehicle.
- `famcal` = a deployable **part** (public render/share surface). No THE-LAW / AGENTS / loop. The life-os loop *operates* it.
- Graduate to its own vehicle later only if coparenting grows heavy machinery (structured comms, disputes, counsel).

## Source-of-truth → artifact flow
```
life-os/parenting/schedule.json   (TRUTH: custody pattern, non-sensitive)
life-os/parenting/kids-events.json(TRUTH: appts/activities, non-sensitive)
        │  gen-share-link.mjs
        ▼
famcal  #view& link  ──►  compare.html / direct share to coparent
```
Change state → run generator → update famcal links → re-share. One truth, generated views.

## Files to create (in life-os) — proposed
```
life-os/
  parenting/
    README.md            # what this is, privacy boundary, how it maps to famcal
    schedule.json        # custody config (Option A) — SOURCE OF TRUTH
    kids-events.json     # appts/activities (non-sensitive)
    gen-share-link.mjs   # state -> famcal #view& link(s); prints + optionally writes compare.html
  routines/
    coparent-sync.prompt.md   # recurring loop tick (mirrors librarian/scout-brief format)
```
Sensitive specifics (kids' full names, medical, home addresses) stay in **tlwj-os vault**, referenced by pointer — never in life-os or famcal.

### schedule.json (Option A, locked)
```json
{
  "version": 1,
  "parents": [
    { "key": "dad", "name": "Dad", "color": "#0a84ff", "role": "primary_home" },
    { "key": "mom", "name": "Mom", "color": "#bf5af2" }
  ],
  "pattern": "seq",
  "anchorDate": "2026-06-27",
  "anchorParent": 0,
  "seq": [1,1,1,1,0,0,0,0,0,0,1,1,0,0],
  "cycleLabel": "4 nights / 2 nights",
  "effectiveFrom": "2026-06-27",
  "boundaryConvention": "pickup-day-belongs-to-receiving-parent",
  "notes": "Mom: Sat->Wed pickup (4n), then Tue->Thu visit (2n). Sensitive detail in tlwj-os vault."
}
```

### kids-events.json (shape; starts empty, grows ongoing)
```json
[
  { "id": "abc123", "title": "Pediatrician", "calId": "med", "allDay": false,
    "start": "2026-07-09T10:00", "end": "2026-07-09T11:00", "location": "", "notes": "" }
]
```
Matches famcal's event schema exactly → drops straight into the share payload.

## coparent-sync.prompt.md — the loop tick (what it does each run)
1. Read `parenting/schedule.json` + `kids-events.json`.
2. Regenerate the famcal `#view&` link(s); if events changed, update `compare.html` / the live link.
3. Surface: **next handoff date**, swaps in the next 14 days, and any kid event landing on the *other* parent's day (conflict flag).
4. If anything changed since last tick → draft a **short, neutral, calm coparent message** for your review (emotional-balance guardrail: factual, no blame, no JADE).
5. Append a dated line to life-os `HANDOFF.md` so the loop knows where it left off.

Cadence: weekly (e.g., Sunday) + on-demand when you add an event. Fits the existing life-os routine pattern.

## garage-hq registry (minimal)
`famcal` is a **part**, not a vehicle → no new `vehicles:` row. Under the existing `life-os` entry, add a note:
```yaml
  # owns domain: parenting (coparenting calendar)
  # part: chakra-digital/famcal  -> https://chakra-digital.github.io/famcal/ (public render surface)
```

## Migration note
The current `famcal/_gen_link.mjs` (dev tool) is superseded by `life-os/parenting/gen-share-link.mjs`. famcal stays a dumb renderer; all state + generation move to life-os.

## Open questions before executing
- Real parent display names, or keep neutral "Dad"/"Mom"? (neutral recommended for a shared view)
- Confirm boundary convention (pickup day = receiving parent) vs evening-pickup (pickup day stays with sending parent).
- Weekly tick day + whether it auto-updates the live link or just drafts for your approval.
