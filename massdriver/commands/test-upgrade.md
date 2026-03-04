---
name: test-upgrade
description: Test a bundle version upgrade by cloning production package config to a test environment
argument-hint: <package-id> <target-version>
allowed-tools:
  - Task
  - Read
  - AskUserQuestion
---

# Upgrade Testing

Start the upgrade-tester agent to validate a bundle version upgrade against production-like configuration.

## What This Does

1. **Identify package** - Package ID specifies exactly which bundle instance to clone (e.g., `api-prod-database`)
2. **Fork environment** - Create isolated test environment from that package's environment
3. **Adjust scale** - Optionally reduce resource sizes for non-critical dependencies
4. **Baseline deploy** - Deploy current version to establish baseline
5. **Upgrade** - Apply target version and validate
6. **Report results** - Summary of upgrade success/failure with recommendations

## Usage

Specify the package ID and target version:

```
/massdriver:test-upgrade api-prod-database 1.3.0
```

```
/massdriver:test-upgrade ecomm-production-redis 2.0.0
```

Package IDs follow the format: `{project}-{environment}-{manifest}` (e.g., `api-prod-database`).

If package ID or version aren't specified, the agent will ask.

## Instructions

Use the Task tool to spawn the `upgrade-tester` agent with the upgrade details. The agent will handle the interactive workflow.

Parse the arguments to extract:
- Package ID (first argument) - identifies the specific bundle instance to clone
- Target version (second argument) - the version to upgrade to

If arguments weren't provided, ask the user for the package ID and target version.
