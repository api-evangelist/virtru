---
name: Author an attribute-based access policy
description: Create a namespace, define an attribute and its values, and map subjects to a value so the Data Security Platform can enforce ABAC on TDF-protected data.
api: openapi/virtru-policy-attributes-openapi.yaml
operations:
  - policy.namespaces.NamespaceService.CreateNamespace
  - policy.attributes.AttributesService.CreateAttribute
  - policy.attributes.AttributesService.CreateAttributeValue
  - policy.subjectmapping.SubjectMappingService.CreateSubjectMapping
---

# Author an attribute-based access policy (Virtru DSP / OpenTDF)

Use this to stand up the policy that governs who can decrypt attribute-tagged data.

## Preconditions
- A valid OIDC Bearer token (see `authentication/virtru-authentication.yml`). Send it on every request.
- All calls are HTTP POST to `/<package>.<Service>/<Method>` with header `Connect-Protocol-Version: 1`.

## Steps
1. **Create the namespace** — `policy.namespaces.NamespaceService.CreateNamespace` with the FQDN that scopes your attributes (e.g. `example.com`).
2. **Define the attribute** — `policy.attributes.AttributesService.CreateAttribute` under that namespace, choosing a rule (`ALL_OF`, `ANY_OF`, or `HIERARCHY`).
3. **Add values** — `policy.attributes.AttributesService.CreateAttributeValue` for each allowed value (e.g. `Confidential`, `Public`).
4. **Map subjects** — `policy.subjectmapping.SubjectMappingService.CreateSubjectMapping` binding a subject condition set + permitted actions to the attribute value, so matching entities are entitled.

## Rules
- Errors return the Connect envelope (`errors/virtru-problem-types.yml`); `already_exists` means the FQN is taken, `permission_denied` means your token lacks admin rights.
- There is no hard delete — use `DeactivateAttribute` / `DeactivateAttributeValue` to retire policy (see `conventions/virtru-conventions.yml`).
