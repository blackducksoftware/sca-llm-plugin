---
name: vuln-triage
description: >
  Targeted vulnerability triage: look up one or a few specific
  vulnerabilities in a Black Duck SCA project, build the customer-
  facing remediation comment from the canonical templates, and submit
  a single batched update_vulnerability_remediation call. Use when
  the user wants to mark specific CVEs as NOT_AFFECTED / MITIGATED /
  IGNORED / PATCHED / UNDER_INVESTIGATION with a proper justification
  and comment. For the full scan→analyze→fix loop, use sca-remediate
  instead.
---

# Vulnerability Triage

Mark specific vulnerabilities with the appropriate remediation status
in Black Duck SCA, using the canonical comment templates and a single
batched update call.

For analysis, prioritization, reachability, and upgrades, see the
`sca-remediate` orchestrator and its subagents. This skill is for the
targeted case where the user already knows which CVE(s) to triage.

The keywords MUST, MUST NOT, SHOULD, SHOULD NOT, and MAY in this
document are to be interpreted as described in RFC 2119.

## Workflow

### Step 1: Verify connectivity

If no Black Duck SCA tools have been successfully called in this
session, MUST call `check_status`. MUST NOT proceed if connectivity
cannot be established.

### Step 2: Identify the target

MUST gather, from the user or from prior session context:
- Project name (use `search_projects_versions` if needed)
- One or more vulnerabilities to triage (CVE IDs)
- Each vulnerability's affected component(s)

MAY use `fetch_project_vulnerabilities` to confirm the vulns exist on
the project, and `vulnerabilities://{vuln_id}` for CVE details when
the user wants to see the technical description before deciding.

### Step 3: Decide status + justification

MUST confirm with the user which `remediation_status` to apply.
Valid statuses:

| Status | Use when |
|--------|----------|
| `NEW` | Not yet evaluated (rarely set manually) |
| `NEEDS_REVIEW` | Flagged for review |
| `UNDER_INVESTIGATION` | Currently being analyzed |
| `REMEDIATION_REQUIRED` | Fix is needed and planned |
| `REMEDIATION_COMPLETE` | Fix has been applied |
| `PATCHED` | Vendor patch applied |
| `MITIGATED` | Risk reduced via other controls |
| `IGNORED` | Accepted risk |
| `DUPLICATE` | Duplicate of another finding |
| `NOT_AFFECTED` | Not exploitable in this context |
| `AFFECTED` | Confirmed exploitable |

`remediation_justification` is only valid for `NOT_AFFECTED` and
`AFFECTED`. See `update_vulnerability_remediation` tool docs for the
full justification matrix.

### Step 4: Build the comment

MUST `Read` `${CLAUDE_PLUGIN_ROOT}/knowledge/triage/comment-templates.md`
and select the template that matches the chosen status + justification:

| Status | Justification | Template |
|--------|---------------|----------|
| NOT_AFFECTED | NO_COMPONENT | NO_COMPONENT |
| NOT_AFFECTED | NO_CODE | NO_CODE |
| NOT_AFFECTED | NOT_EXECUTED | NOT_EXECUTED |
| NOT_AFFECTED | NOT_CONTROLLED | NOT_CONTROLLED |
| NOT_AFFECTED | ALREADY_MITIGATED | ALREADY_MITIGATED |
| UNDER_INVESTIGATION | — | UNDER_INVESTIGATION |
| REMEDIATION_COMPLETE | — | VENDOR_FIX |
| MITIGATED / IGNORED / etc. | — | Use the matching AFFECTED-family template |

Fill `{placeholders}` from the evidence the user provides (component
name, version, evidence method, rationale, etc.).

### Step 5: Source `related_vulnerability`

For each vuln, MUST read
`project-vulnerabilities://{project_id}/{version_id}` and take
`related_vulnerability` from the project-scoped CSV.

- MUST OMIT `related_vulnerability` when the column is empty.
- MUST NOT derive it from the global `vulnerabilities://{vuln_id}`
  resource links — those are global mappings, not scoped to the
  project. Passing a mismatched related ID causes the BDSCA PUT to
  silently no-op (no change applied, no error raised).

### Step 6: Batched update

MUST collect all triage items into a single
`update_vulnerability_remediation` call using the `relations` list,
one entry per vulnerability. Each entry includes `vulnerability`,
`component_id`, `component_version_id`, and `related_vulnerability`
(omit if empty).

Call once with `remediation_status`, optional
`remediation_justification`, and the templated `comment`. MUST confirm
the batch with the user before submitting.

### Step 7 (optional): Cross-project impact

MAY use `search_vulnerabilities` with `affecting_projects=True` or
`search_components` to surface which other projects use the same
vulnerable component version. SHOULD prioritize wide-blast-radius
findings for the user's follow-up work.

## Output Format

MUST present:
- Vulns triaged (CVE, component, new status, justification)
- The comment text submitted (one block per template used)
- Any vulns skipped + reason

## Constraints

- MUST confirm with the user before submitting any remediation
  change.
- MUST use the templates in
  `${CLAUDE_PLUGIN_ROOT}/knowledge/triage/comment-templates.md`
  rather than free-form comments.
- MUST batch all changes into a single `update_vulnerability_remediation`
  call when triaging more than one vuln at a time.
- SHOULD suggest `sca-remediate` when the user wants the full
  scan→analyze→fix loop rather than targeted triage.
- SHOULD suggest `sca-report` for formal reporting (SBOM/VEX/Notices)
  after a triage round.
