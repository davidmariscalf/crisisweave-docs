# Threat model

CrisisWeave handles information that can influence attention during fast-moving incidents. The main risks are therefore not limited to classic software compromise; bad, stale or duplicated information can also cause harm.

## Assets to protect

- provenance of each report
- integrity of normalized events
- distinction between official and unofficial sources
- freshness timestamps
- availability of the last known useful view
- auditability of merge and alert decisions

## Threats

### 1. Fabricated or manipulated reports

An attacker can publish plausible crisis claims through community feeds or compromised sources.

Mitigations:

- never infer `official=true` from content alone
- retain source identifiers and URLs
- count independent sources rather than repeated copies from one source
- expose evidence used to raise confidence
- keep confidence described as a ranking signal rather than truth probability

### 2. Duplicate amplification

The same original claim can be syndicated across many sites and look like independent confirmation.

Mitigations:

- aggregate strongest evidence by source identity
- add future source-lineage detection for syndicated feeds
- retain merged source IDs for audit

### 3. Location spoofing or ambiguous geocoding

Wrong coordinates can direct users toward the wrong place.

Mitigations:

- do not invent geometry when the source lacks it
- validate longitude/latitude ranges
- treat textual area and point geometry as separate fields
- require future geocoding enrichments to record method and confidence

### 4. Stale offline data

Cached information can remain visible after conditions have changed.

Mitigations:

- cache last useful data but preserve timestamps
- production UIs must show last successful refresh time
- expired events should remain distinguishable from active events
- never treat offline silence as confirmation that a crisis ended

### 5. Malicious feed payloads

Feeds can contain oversized records, malformed XML/JSON or HTML intended for injection.

Mitigations:

- enforce request and payload limits in production
- escape event text before inserting it into map popups
- parse data rather than executing embedded content
- avoid fetching arbitrary resources referenced from untrusted payloads

### 6. Alert storms

A broad rule or repeated event can trigger excessive downstream notifications.

Mitigations:

- stable `rule_id + event_id` fingerprints
- transport layer should enforce idempotency and cooldowns
- alerts remain candidates until delivery policy accepts them

### 7. Dependency or supply-chain compromise

Browser libraries and future Python dependencies can be compromised upstream.

Mitigations:

- keep the critical MVP dependency-light
- pin versions in production
- use lockfiles and integrity checks where supported
- review dependency additions instead of treating repository count as a quality metric

### 8. Core service abuse

The MVP core has no authentication and stores data in memory.

Mitigations before internet deployment:

- authentication and authorization
- TLS behind a hardened reverse proxy
- durable database with migrations
- rate limits and source quotas
- append-only audit records
- idempotent ingestion keys
- request size and schema validation
- monitoring and backups

The current core should therefore be treated as a local integration harness, not an internet-facing emergency service.

## Failure philosophy

When CrisisWeave cannot establish a fact safely, it should preserve uncertainty. Unknown is preferable to a fabricated coordinate, a hidden source, or a confidence score that looks more precise than the evidence warrants.
