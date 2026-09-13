# Architecture

## Problem statement

Crisis information is usually fragmented across official CAP alerts, agency APIs, RSS feeds, local media, sensors and volunteer reports. A useful system has to normalize those sources without erasing provenance, detect likely duplicate reports, surface uncertainty and continue to provide context when connectivity degrades.

CrisisWeave treats that as a data-fusion problem rather than primarily a mapping problem.

## Event flow

```text
CAP / RSS / JSON / field reports
              |
              v
      crisisweave-ingests
              |
      normalized event JSON
              |
              v
       crisisweave-verify
              |
  merged events + evidence trail
       /             \
      v               v
crisisweave-map   crisisweave-alerts
      |
      v
crisisweave-offline
```

`crisisweave-sim` can feed synthetic events into the same pipeline for repeatable tests.

## Public event contract

The canonical schema is stored in the umbrella `CrisisWeave` repository. Public modules must not require the private core repository to understand or exchange events.

An event carries:

- stable event identifier
- event kind
- title and description
- observation time and optional expiry
- severity and confidence in the normalized 0..1 range
- official/unofficial source flag
- optional Point geometry and textual area
- source name, type, URL and source identifier
- evidence records
- original/raw context when safe to preserve
- tags

## Ingestion boundary

Ingest adapters should parse and normalize, not verify. In particular, they must not:

- fabricate coordinates
- silently discard source identifiers
- mark non-official feeds as official merely because they look authoritative
- transform malformed timestamps into precise claims without preserving that uncertainty

## Verification boundary

The first verifier is deterministic. It uses event-kind compatibility, textual overlap, time proximity, geographic proximity and area overlap to propose duplicate clusters. It then aggregates independent source evidence with diminishing returns.

The confidence score is a ranking signal. It is explicitly not a calibrated probability of truth.

A future ML or embedding-based candidate generator may be added before deterministic verification, but it should not remove the auditable rules from the final merge decision without strong evaluation.

## Presentation boundary

The map renders only valid Point geometry. Unknown locations remain unknown. This prevents a visually attractive but operationally dangerous failure mode where ambiguous text is turned into a confident marker.

## Offline behavior

The offline layer keeps the application shell and last successful event response. It does not attempt to automatically mirror an unbounded tile set. Cached data must be shown with freshness information in production.

## Alerting boundary

Alert rules are explicit JSON objects. Rule evaluation produces alert candidates; transport is kept separate so deployments can attach email, SMS, push, radio gateways or other delivery systems without changing matching behavior.

## Private core

`crisisweave-core` currently provides only lightweight orchestration. Production concerns such as durable storage, authentication, per-source rate limiting, audit logs, idempotency and access control are intentionally not disguised as solved problems.

## Useful upstream ideas

The design deliberately borrows proven patterns from mature open-source ecosystems without copying large codebases into this project:

- MapLibre for standards-based browser mapping
- H3-style indexing for future spatial candidate generation
- RapidFuzz-style lexical candidate matching for future scale-up
- FastAPI/Pydantic-style explicit API contracts if the core later adopts dependencies
- browser service workers for constrained offline operation
- CAP as an interoperable official alert format

The integration boundary matters more than the number of dependencies. CrisisWeave should only add an upstream project when it removes real complexity or improves reliability.
