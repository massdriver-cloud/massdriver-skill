---
name: gen
description: Quickly generate a bundle scaffold without the full interactive deploy loop
argument-hint: <use-case description>
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - AskUserQuestion
---

# Quick Bundle Generation

Generate a Massdriver bundle scaffold based on a use case description. This is the non-interactive "quick gen" mode - it creates the bundle files and hands off to you for testing.

## What This Does

1. Ask a few quick questions about the bundle
2. Generate the complete bundle structure
3. Run local validation (`mass bundle build`, `tofu validate`)
4. Hand off to you for deployment and testing

## Usage

```
/massdriver:gen RDS MySQL database for OLTP workloads

/massdriver:gen Lambda function with API Gateway trigger

/massdriver:gen S3 bucket for data lake landing zone
```

## Instructions

### 1. Quick Requirements

Ask the user (if not clear from the description):
- Cloud provider (AWS, GCP, Azure)?
- What connections does it need (network, credentials)?
- What artifact does it produce?
- Any specific presets they want?

### 2. Generate Bundle Structure

Create the bundle in `bundles/<bundle-name>/`:

```
bundles/<bundle-name>/
├── massdriver.yaml     # Bundle manifest with params, connections, artifacts
├── README.md           # Bundle documentation
├── operator.md         # Runbook template
└── src/
    ├── main.tf         # Primary Terraform code
    ├── artifacts.tf    # massdriver_artifact resources
    └── .checkov.yml    # Checkov skip rules (if any)
```

### 3. Apply Best Practices

- **Params**: Focus on 3-5 developer-facing questions, use presets
- **80/20 rule**: Cover common use cases, don't over-generalize
- **Connections**: Use existing artifact definitions (`mass def list`, ignore any `massdriver/` prefixed results)
- **Artifacts**: Match the artifact definition schema exactly
- **Provider**: `mass def get <platform>` FIRST, then write provider using only those fields — artifact defs and providers are 1:1
- **Compliance**: Default to secure settings, add `halt_on_failure` for prod

### 4. Local Validation

Run validation:
```bash
cd bundles/<bundle-name>
mass bundle build
cd src && tofu init && tofu validate
```

Fix any issues before finishing.

### 5. Hand Off

Tell the user:
- Where the bundle was created
- How to test it: `mass bundle publish --development`
- Remind them to set release channel to `latest+dev` or `~X.Y+dev` after creating a package
- If they need new artifact definitions, publish them first: `mass definition publish artifact-definitions/<name>/massdriver.yaml` (no --development flag, goes live immediately)
- Suggest using `/massdriver:develop` if they want the full test loop
