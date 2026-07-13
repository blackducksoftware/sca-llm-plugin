# SCA Skills & Agents Reference

Entry-point reference for the Black Duck SCA workflows shipped with
this plugin. Each skill and agent owns its own description in its
frontmatter — this file is only a map.

## Surface

**Skills** are user-invoked entry points or stateful orchestrators.

| Kind | Name | Purpose |
|------|------|---------|
| **Skill** | `sca-scan` | Scan a file, directory, or container image |
| **Skill** | `vuln-triage` | Targeted triage for known CVE(s) using comment templates |
| **Skill** | `license-compliance` | Review license risks and policy compliance |
| **Skill** | `sca-report` | Generate SBOM, VEX/CSAF, or Notices reports |


## Workflow Map

```
                ┌──────────┐
                │ sca-scan │  Scan a project
                └────┬─────┘
                     │
          ┌──────────┼─────────────────┐
          ▼          ▼                 ▼
  ┌────────────┐ ┌──────────┐ ┌──────────────────────┐
  │ vuln-triage│ │ license- │ │   sca-report         │
  │ (targeted) │ │compliance│ │ SBOM / VEX / Notices │
  └────────────┘ └──────────┘ └──────────────────────┘

```

## Knowledge files

Reference data loaded by skills/agents via `Read`. Not auto-discovered as skills.

| Path | Content |
|------|---------|
| `knowledge/triage/comment-templates.md` | 11 BD Hub remediation comment templates (5 NOT_AFFECTED justifications, 5 AFFECTED remediations, 1 UNDER_INVESTIGATION) |
