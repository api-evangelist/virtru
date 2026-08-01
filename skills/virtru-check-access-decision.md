---
name: Check an access decision and rewrap a key
description: Ask the Authorization service whether an entity may access a resource, then obtain the KAS public key and rewrap to decrypt a TDF.
api: openapi/virtru-authorization-v2-openapi.yaml
operations:
  - authorization.v2.AuthorizationService.GetEntitlements
  - authorization.v2.AuthorizationService.GetDecision
  - kas.AccessService.PublicKey
  - kas.AccessService.Rewrap
---

# Check an access decision and rewrap a key (Virtru DSP / OpenTDF)

Use this at read/decrypt time to enforce attribute-based access on protected data.

## Preconditions
- OIDC Bearer token for the requesting entity (`authentication/virtru-authentication.yml`).

## Steps
1. **(Optional) Inspect entitlements** — `authorization.v2.AuthorizationService.GetEntitlements` to see the attribute values the entity holds.
2. **Get the decision** — `authorization.v2.AuthorizationService.GetDecision` with the entity + resource attributes + action; a `permit` is required before any key release.
3. **Fetch the KAS public key** — `kas.AccessService.PublicKey` for the Key Access Server referenced by the TDF.
4. **Rewrap** — `kas.AccessService.Rewrap` to unwrap the data key; the KAS re-checks ABAC and only rewraps on a permit.

## Rules
- A denied decision surfaces as `permission_denied` in the Connect error envelope — do not retry without new entitlements.
- Use v2 (`authorization.v2`) operations; v1 is retained but superseded (`lifecycle/virtru-lifecycle.yml`).
