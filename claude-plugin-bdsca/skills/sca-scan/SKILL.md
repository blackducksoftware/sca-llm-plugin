---
name: sca-scan
description: >
  Step 1 of the SCA workflow: scan a file, directory, or container image
  with Black Duck SCA and interpret the results. Use when the user wants
  to scan, upload, or onboard a project, binary, SBOM, or container.
  After scanning, suggest vuln-triage for security review or
  license-compliance for license analysis.
---

# Project Scan

Guide the user through scanning a file with Black Duck SCA and
interpreting the results.

The keywords MUST, MUST NOT, SHOULD, SHOULD NOT, and MAY in this document
are to be interpreted as described in the RFC 2119.

## Workflow

### Step 1: Verify connectivity

If no Black Duck SCA tools have been successfully called in this session,
MUST call `check_status` to confirm the backend is reachable. If a tool
call fails with a connection error, SHOULD retry after calling
`check_status` to diagnose the issue. MUST NOT proceed if connectivity
cannot be established.

### Step 2: Identify the scan target

MUST ask the user what they want to scan. The `scan` tool supports:
- **Source directories** — project directories with source code
- **SBOM files** (SPDX, CycloneDX)
- **Binary files** (executables, libraries)
- **Container images** (Docker tar files)
- **BDIO files** (Black Duck I/O format)

The tool auto-detects the file/directory type when `scan_type` is `auto`.

### Step 3: Read scan workflow instructions

MUST read `workflow-instructions://scan` before calling the `scan` tool.
This resource explains how to choose the right `scan_type`, how project
metadata (name, version) is auto-derived, and the recommended workflow.

### Step 4: Confirm metadata and scan

The `scan` tool auto-derives project name and version from the target
(git remote, package manifests, filenames, etc.). With `confirm_metadata`
enabled (the default), MUST present the derived metadata to the user for
confirmation before the scan proceeds.

SHOULD inform the user that processing may take several minutes for large
files or directories.

### Step 5: Find the project

After scanning, MUST use `search_projects_versions` to locate the newly
created or updated project. The scan results are associated with a
project version.

### Step 6: Get the BOM

MUST call `fetch_project_components` to retrieve the Bill of Materials.
This shows all detected components with their versions and license
information.

### Step 7: Get vulnerability summary

MUST call `fetch_project_vulnerabilities` to get the security overview.

## Output Format

MUST structure the scan results as:
- **Executive Summary** — 2-3 sentence overview of findings
- **Component count** — total detected components
- **Vulnerability breakdown** — critical/high/medium/low counts
- **License risks** — any restrictive or conflicting licenses
- **Policy violations** — any policy rules triggered
- **Recommendations** — prioritized next steps

## Constraints

- MUST mention the project name for future reference if the scan creates
  a new project.
- SHOULD suggest the vuln-triage skill if significant security issues
  are found.
- SHOULD suggest the license-compliance skill if license risks are
  identified.
- SHOULD suggest the sca-report skill if the user wants a formal summary
  or needs to export reports.
