# CrisisWeave deployment tiers

CrisisWeave deliberately separates technical release quality from permission to use real crisis data. Passing CI is necessary, but it is not sufficient for a sensitive operational deployment.

## Tier 1: synthetic public evaluation

Purpose: demonstrate the product, exercise the full information flow and collect feedback without handling sensitive operational data.

Requirements:

- reviewed component revisions pinned by the umbrella lock file
- umbrella production preflight and cross-repository E2E passing
- synthetic or explicitly public evaluation data only
- public worksite export contract enforced
- clear uncertainty, provenance and freshness information in the UI

This tier does not require a paid alerting service or a real emergency-operations identity provider.

## Tier 2: controlled operator pilot

Purpose: let a small authorised team evaluate workflows with approved sources while keeping the deployment reversible and closely supervised.

Additional requirements:

- named incident-response, privacy and backup owners
- HTTPS and operator-owned DNS
- strong distinct service credentials
- tested encrypted backup and restore path
- agreed data-retention policy
- an approved source/provider inventory
- operator access controls appropriate to the information being handled
- monitoring and a tested alert path if the pilot depends on timely operational response

The pilot must still preserve the public/private worksite boundary and must not turn alerts automatically into volunteer instructions.

## Tier 3: sensitive operational deployment

Purpose: handle real operational information where disclosure, corruption, staleness or downtime could materially affect people or partner organisations.

Additional requirements:

- organisation-owned identity and MFA where sensitive operator access is enabled
- documented on-call and incident escalation process
- recovery key stored off-host with a successful recovery drill
- real alert receiver owned by the operating organisation when paging is required
- legal and contractual approval for live providers and data handling
- change control for production revisions and protected main branches
- a rollback procedure tested against exact component SHAs

The `crisisweave-infra` go-live check is intentionally fail-closed for operator-owned requirements that code cannot safely invent.

## Stop conditions

Do not promote a deployment when required owners are unnamed, DNS or TLS is not under operator control, recovery has not been tested, a sensitive provider has not been approved, or the locked cross-repository test suite is failing.
