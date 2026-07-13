---
name: license-compliance
description: >
  Step 2b of the SCA workflow: review license risks and policy compliance
  for a Black Duck SCA project. Use when the user asks about licenses,
  copyleft risk, license conflicts, compliance status, or policy
  violations. After review, suggest sca-report to generate a summary or
  export SBOM/VEX/Notices reports.
---

# License Compliance Review

Guide the user through analyzing license risks and policy compliance for
a Black Duck SCA project.

The keywords MUST, MUST NOT, SHOULD, SHOULD NOT, and MAY in this document
are to be interpreted as described in the RFC 2119.

## Workflow

### Step 1: Verify connectivity

If no Black Duck SCA tools have been successfully called in this session,
MUST call `check_status` to confirm the backend is reachable. If a tool
call fails with a connection error, SHOULD retry after calling
`check_status` to diagnose the issue. MUST NOT proceed if connectivity
cannot be established.

### Step 2: Find the target project

MUST ask the user which project to review. MUST use
`search_projects_versions` to find it. If multiple matches, MUST present
them and let the user choose.

### Step 3: Fetch license risk summary

MUST read `license-risk-summary://{projectId}/{projectVersionId}` to get
the license risk overview. This is lightweight and provides license
distribution, risk levels, and conflict indicators without fetching the
full BOM.

MUST NOT call `fetch_project_components` unless the user requests
per-component license details.

### Step 4: Analyze licenses

MUST summarize the license landscape:
- **Permissive** (MIT, Apache-2.0, BSD) — low risk
- **Copyleft** (GPL, LGPL, AGPL) — may require source disclosure
- **Proprietary/Unknown** — requires review
- **Conflicts** — incompatible license combinations

SHOULD call `fetch_policy_violation_status` to check for policy-level
license violations that the license risk summary alone may not surface.

### Step 5: Per-component details (optional)

If the user wants to see which specific components carry which licenses,
MUST call `fetch_project_components` to get the full BOM with license
data.

## Output Format

MUST structure the compliance summary as:
- **Executive Summary** — 2-3 sentence compliance overview
- **License Distribution** — breakdown by license family
- **Risk Findings** — copyleft, unknown, or conflicting licenses
- **Policy Violations** — any policy rules triggered
- **Recommendations** — prioritized actions for resolving issues

## Constraints

- MUST highlight copyleft licenses as they have the most impact on
  distribution.
- SHOULD suggest the sca-report skill as a next step if the user wants
  to export SBOM, VEX/CSAF, or Notices reports.
