---
name: sca-report
description: >
  Step 3 of the SCA workflow: summarize findings and optionally export
  reports from Black Duck SCA. Use when the user asks to generate a
  report, export an SBOM, create a VEX/CSAF document, produce license
  notices, or get a written summary of scan or triage results. Typically
  run after sca-scan, vuln-triage, or license-compliance.
---

# SCA Report

Generate a summary of SCA findings in the chat and optionally export
formal reports (SBOM, VEX/CSAF, Notices) from Black Duck SCA.

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

If a project was already identified in this session (via sca-scan,
vuln-triage, or license-compliance), SHOULD reuse it and confirm with
the user. Otherwise, MUST ask the user which project to report on and
use `search_projects_versions` to find it.

### Step 3: Gather data

MUST collect the data needed for the summary. Depending on what was
already fetched in this session:
- SHOULD call `fetch_project_vulnerabilities` if vulnerability data is
  needed and was not already retrieved.
- For license data: SHOULD delegate to the `license-compliance` skill
  rather than re-deriving the license summary here. Reuse its
  computed risk overview (license distribution, copyleft / unknown /
  conflicts) if the user has already run it in this session;
  otherwise suggest invoking `license-compliance` for the license
  section and proceed with vulnerability + policy data inline.
- SHOULD call `fetch_policy_violation_status` if policy status is
  needed and was not already retrieved.

### Step 4: Present summary in chat

MUST present a structured summary of findings directly in the chat:
- **Executive Summary** — 2-3 sentence overall assessment
- **Vulnerability Overview** — severity breakdown with counts
- **License Overview** — license family distribution and risks
- **Policy Status** — compliance status and any violations
- **Top Risks** — the most critical items requiring attention
- **Recommendations** — prioritized next steps

### Step 5: Export reports (optional)

MUST ask the user if they want to export formal reports. If yes, MUST
ask which report type(s) they need, then call `create_report` for each:
- **SBOM** — Software Bill of Materials (SPDX or CycloneDX format)
- **VEX/CSAF** — Vulnerability Exploitability eXchange document
- **Notices** — License notices file for distribution

For SBOM reports: if no `template_id` is provided, the tool returns a
list of available SBOM templates. MUST present the template names to the
user as selections (MUST NOT show template UUIDs). Once the user selects
a template, MUST call `create_report` again with the corresponding
`template_id`.

MUST report the file path of each generated report to the user.

## Constraints

- MUST NOT include copyright and license text in Notices reports unless
  the user explicitly requests it (generation takes very long).
- SBOM reports are commonly required for supply chain compliance.
- VEX/CSAF reports are useful for communicating vulnerability status to
  downstream consumers.
- Notices reports are required when distributing software containing
  open-source components.
- SHOULD generate multiple report types in a single invocation if the
  user requests them.
