---
name: develop
description: Start interactive bundle development workflow with full deploy loop and compliance remediation
argument-hint: <use-case description>
allowed-tools:
  - Task
  - Read
  - AskUserQuestion
---

# Bundle Development

Start the bundle-dev agent to guide you through creating and testing a Massdriver infrastructure bundle.

## What This Does

1. **Gather requirements** - Understand your use case, developer UX needs, and compliance strategy
2. **Scaffold bundle** - Create massdriver.yaml, Terraform code, and supporting files
3. **Create test environment** - Set up isolated `agent<SUFFIX>` environment in your project
4. **Deploy and test** - Run the full test cycle: clean → apply → clean → apply → teardown
5. **Remediate compliance** - Fix Checkov findings according to your strategy
6. **Journal results** - Record what was tested in the environment description

## Usage

Provide a description of what bundle you want to create:

```
/massdriver:develop PostgreSQL database for application backends with dev/staging/prod presets
```

```
/massdriver:develop S3 bucket for static asset storage with CloudFront CDN
```

```
/massdriver:develop EKS cluster with managed node groups and cluster autoscaler
```

## Instructions

Use the Task tool to spawn the `bundle-dev` agent with the user's use case description. The agent will handle the interactive workflow.

If no use case was provided in the command arguments, ask the user what bundle they want to develop.
