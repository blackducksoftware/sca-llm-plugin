# BD Hub Remediation Comment Templates

Structured comment templates for `update_vulnerability_remediation`.
Use the appropriate template based on the remediation status and
justification. Comments are **customer-facing** — keep them factual,
concise, and free of internal technical details.

## NOT_AFFECTED Templates

### NO_COMPONENT — Component not present

```
Known Not Affected

Justification: Component not present. The vulnerable component is not
included in the product.

How or why is the product not affected: {component} is not part of the
project's dependency tree. Verified via {evidence_method}.
```

### NO_CODE — Vulnerable code not present

```
Known Not Affected

Justification: Vulnerable code not present. The specific vulnerable
code is not present in the component as used by this product.

How or why is the product not affected: The project uses {component}
{version} but does not invoke the vulnerable code path. {evidence}.
```

### NOT_CONTROLLED — Code cannot be controlled by adversary

```
Known Not Affected

Justification: Vulnerable code cannot be controlled by adversary. The
vulnerable code exists but cannot be reached or triggered by an
external attacker in the product's deployment context.

How or why is the product not affected: {explanation of why the code
path is not externally accessible}.
```

### NOT_EXECUTED — Code not in execute path

```
Known Not Affected

Justification: Vulnerable code not in execute path. The vulnerable
code exists but cannot be executed in the product's operational context.

How or why is the product not affected: {component} is a transitive
dependency not imported or invoked by the application code. Verified
via {evidence_method}.
```

### ALREADY_MITIGATED — Inline mitigations exist

```
Known Not Affected

Justification: Inline mitigations already exist. Built-in security
measures are already in place, preventing the exploitation of the
vulnerability.

How or why is the product not affected: {description of existing
mitigation, e.g., input validation, sandboxing, network isolation}.
```

## AFFECTED Templates

### VENDOR_FIX — Upgrade available

```
Known Affected

Remediation: Vendor fix. There is an update by the vendor to address
the vulnerability.

How is it affected: {component} {current_version} is vulnerable to
{CVE_ID}. An upgrade to {fix_version} resolves the issue.

Expected fix date: {date or "targeting next release cycle"}
Customer action until fix: {mitigation advice, e.g., "No action
required — fix PR has been raised."}
```

### WORKAROUND — Workaround available

```
Known Affected

Remediation: Workaround. There is a workaround available to mitigate
the vulnerability without upgrading.

How is it affected: {component} {current_version} is vulnerable to
{CVE_ID}.

Workaround: {description of the workaround}.
Customer action until fix: Apply the workaround described above.
```

### MITIGATION — Built-in protections

```
Known Affected

Remediation: Mitigation. The product includes built-in protections
that reduce the risk of exploitation.

How is it affected: {component} {current_version} is vulnerable to
{CVE_ID}, but existing controls limit the attack surface.

Mitigation details: {description of existing protections}.
```

### NO_FIX_PLANNED — Will not be fixed

```
Known Affected

Remediation: No fix planned. The vulnerability will not be fixed in
this version.

How is it affected: {component} {current_version} is vulnerable to
{CVE_ID}.

Reason: {why no fix — e.g., "component is being deprecated", "risk
accepted per policy", "no upstream fix available"}.
```

### NONE_AVAILABLE — No fix exists

```
Known Affected

Remediation: None available. There are no fixes available for this
vulnerability at this time.

How is it affected: {component} {current_version} is vulnerable to
{CVE_ID}. No vendor patch or upgrade resolves this issue.

Customer action until fix: {mitigation advice}.
```

## UNDER_INVESTIGATION Template

```
Under Investigation

{component} {current_version} is being evaluated for {CVE_ID}.
Analysis is in progress. Status will be updated once a determination
is made.
```

## Usage Notes

- Replace `{placeholders}` with actual values.
- `{evidence_method}` examples: "dependency tree analysis",
  "pipdeptree --reverse", "go mod why", "npm why", "ldd analysis",
  "import grep with no matches", "build-tool verification".
- `{evidence}` should be a brief factual statement, not raw tool
  output. Raw output belongs in PR descriptions, not BD Hub comments.
- For batch operations, use the same template for all items in the
  batch, varying only the component-specific fields.
