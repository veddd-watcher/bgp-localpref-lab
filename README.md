# BGP LOCAL_PREF Overriding AS_PATH

Day 8 lab. Containerlab + FRR 10.2.1. Reuses the Day 7 triangle.

## Topology
Three routers in a triangle: r1 (AS 65001), r2 (AS 65002), r3 (AS 65003).
Each router originates its loopback (1.1.1.1/32, 2.2.2.2/32, 3.3.3.3/32).

## What I changed
On r3 only, an inbound route-map on the eBGP neighbor r2 (10.0.23.1):

    route-map PREFER-R2 permit 10
     set local-preference 200

    neighbor 10.0.23.1 route-map PREFER-R2 in

## Result (1.1.1.1/32 on r3)
- Before: best path 65001 (direct from r1). LOCAL_PREF tied at 100, so shorter AS_PATH won.
- After: best path 65002 65001 (via r2), LocPrf 200. LOCAL_PREF is checked before AS_PATH, so the longer path won.

## Bonus finding (Day 6 PfxSnt question)
r1 advertises 3 prefixes to r3, including r3's own 3.3.3.3/32.
r3 rejects it with the AS_PATH loop check (it sees its own AS 65003 in the path).
A router sends its best path for every prefix to each neighbor, not only what it originated.

## Evidence files
- 04-r3-before-localpref.txt
- 05-r3-after-localpref.txt
- 06-r1-advertised-to-r3.txt
