# crisisweave-docs

Design notes for CrisisWeave, a crisis-information pipeline built around provenance, explainable verification and graceful degradation when connectivity is poor.

## Documents

- [`ARCHITECTURE.md`](ARCHITECTURE.md): component boundaries, event flow and public contract.
- [`THREAT_MODEL.md`](THREAT_MODEL.md): misinformation, malicious inputs, stale offline data and operational failure modes.

## Core principle

CrisisWeave should make uncertainty visible rather than hiding it. A report can be useful before it is fully corroborated, but the interface must preserve where it came from, whether it is official, how many independent sources support it and how old the information is.

## Non-goals for the MVP

- autonomous evacuation or rescue decisions
- replacing official emergency channels
- guessing coordinates from ambiguous text
- claiming probabilistic truth from a heuristic confidence score
- bulk mirroring of map tiles without storage and freshness controls

The project is intentionally modular so individual parts can be replaced by stronger implementations without changing the public event contract.
