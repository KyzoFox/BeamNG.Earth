Title: Bridges: a hard minimum clearance under every overpass (trucks no longer hit bridges)

Players keep reporting on the Discord that trucks hit overpasses. This PR fixes that on top of 1.6.0 beta. It only changes bridge code: server/grading.py, server/roads.py, server/roadmesh.py and their tests. Your 1.6.0 fixes are kept as they are, including _lower_underpasses (#138, #142).

## What it does
- **A hard minimum under every overpass: 5.0 m (16.4 ft) under the beams.** This applies anywhere in the world, over every road a bridge crosses (DECK_HARD_CLEAR = 6.6 m to the deck surface). DECK_ROAD_CLEAR stays the aim. Where the ramps cannot lift a deck enough, it climbs steeper to make the minimum: up to 25 % on a street's bridge, 8 % on a motorway's or trunk road's (driven fast; there the road under it dips instead).
- **Ramps lift properly.**
  - On a divided road, the twin bridge no longer caps the other carriageway's ramp.
  - The ramp is measured from the road's own planned height, not the raw ground. Smoothing had cut embankments metres down.
  - Frontage roads joining at a bridge end no longer hold it down.
  - A raised road always stands on its fill.
- **Ramps no longer go missing when a neighbouring map cell fails to load.** Only that cell is skipped, and the area is planned again a minute later.
- **Roads under long interstate viaducts get the rest of the dip.** This runs after 1.6.0's own short-bridge dip and only where clearance is still missing: at most 6 m, on 6 % slopes.
- **Downtown: the interstate dips under a street's bridge whose ramps can't rise** (frontage roads at its ends; Shreveport's streets over I-49): at most 4 m, on 3 % slopes, and its links go down with it.
- **No more decks ending in the air where ramps fork** (I-635 at I-20, Dallas).
- **A short overpass is no longer taken for a junction**, and ramp fill is kept off roads passing under the bridge.
- **Thinner deck slab:** 0.6 m, plus 1.0 m of girders.

## Tested
I checked every bridge-over-road crossing in the map data of I-20 through Louisiana, Shreveport and Dallas (I-20 / I-635).

| Area | Crossings under 5 m, 1.6.0 beta | With this PR |
|---|---|---|
| Louisiana | 67 | 2 |
| Shreveport | 39 | 6 |
| Dallas | 25 | 0 |

The bridges players reported (clearance under the beams):

| Bridge | 1.6.0 beta | With this PR |
|---|---|---|
| Nutland Rd, Monroe | 3.0 m | 5.8 m |
| Thomas Rd, West Monroe | 4.8 m | 6.0 m |
| Stella St, West Monroe | 3.9 m | 5.6 m |
| I-20, Ruston | 4.4 m | 5.1 m |
| Greene County, AL | −1.0 m | 6.1 m |

I also drove from Louisiana to Dallas on the earlier build of this fix and cleared every bridge.

Known spots left:
- Monroe's I-20 / US 165 interchange: ramps pass under I-20 right at its bridge end.
- Downtown Shreveport near I-20 / I-49: a ramp climbing onto another bridge passes under I-20 (also in 1.6.0 beta).

The server tests for grading, roads, roadmesh, approaches, bridgetypes, furniture and tunnels all pass.

KyzoFox
