---
# Massdriver Plugin Settings
#
# Copy this file to your project's .claude/ directory:
#   cp templates/massdriver.local.md .claude/massdriver.local.md
#
# These settings customize how the Massdriver plugin behaves.

# CLI profile to use (from ~/.massdriver/config.yaml)
# Leave blank to use default profile
mass_profile: ""

# Regex pattern to identify production environments
# The plugin will BLOCK any commands targeting environments matching this pattern
# Examples:
#   - "prod" matches: myapp-prod-db, production, prod-east
#   - "(prod|production)" matches: prod OR production
#   - "prd-.*" matches: prd-east, prd-west
production_pattern: (prod|production)

# Default organization ID (optional)
# If set, agents won't need to ask for it
organization_id: ""

# Default project for test environments (optional)
# If set, bundle-dev agent will create test envs here
default_test_project: ""
---

# Massdriver Settings

This file configures the Massdriver plugin for this project.

## Production Protection

Environments matching the `production_pattern` above are protected:
- Cannot deploy to them
- Cannot configure packages in them
- Cannot decommission resources in them

Read-only operations (viewing logs, artifacts) are still allowed.

## Test Environment Naming

The plugin creates test environments with the pattern `agent<RANDOM>`:
- `agentX7K2M9` - 6 random alphanumeric characters
- These environments are yours to manage
- Set descriptions on them as a journal of what was tested

## Profile Configuration

If you have multiple Massdriver CLI profiles, set `mass_profile` to the one
this project should use. Profiles are defined in `~/.massdriver/config.yaml`.
