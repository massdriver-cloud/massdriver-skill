# Massdriver Plugin for Claude Code

Build infrastructure bundles for [Massdriver](https://massdriver.cloud), the internal developer platform that turns infrastructure-as-code into reusable, self-service components with built-in guardrails.

## Installation

```bash
# Add the marketplace
/plugin marketplace add massdriver-cloud/claude-plugins

# Install the plugin
/plugin install massdriver@massdriver-cloud-claude-plugins
```

## Commands

### `/massdriver:develop` - Full Bundle Development

Interactive workflow for creating and testing bundles with deploy loop and compliance remediation.

```
/massdriver:develop PostgreSQL database for application backends with dev/staging/prod presets
```

```
/massdriver:develop S3 bucket for static asset storage with CloudFront CDN
```

**What it does:**
1. Gathers your design intent (UX, constraints, connections)
2. Scaffolds the bundle with best practices
3. Creates isolated test environment (`agent<SUFFIX>`)
4. Runs test cycle: clean → apply → clean → apply → teardown
5. Remediates compliance findings automatically
6. Journals results in environment description

### `/massdriver:test-upgrade` - Day 2 Upgrade Testing

Validate bundle version upgrades by cloning production package config to a test environment.

```
/massdriver:test-upgrade api-prod-database 1.3.0
```

Package IDs follow the format `{project}-{environment}-{manifest}`.

**What it does:**
1. Forks the package's environment (you choose what to copy)
2. Optionally scales down non-critical dependencies
3. Deploys current version as baseline
4. Upgrades to target version and validates
5. Reports success/failure with recommendations

### `/massdriver:gen` - Quick Scaffolding

Generate a bundle without the deploy loop.

```
/massdriver:gen RDS MySQL for OLTP workloads
```

## What This Plugin Does

This plugin helps platform engineers create and test Massdriver bundles—reusable IaC modules that package Terraform, OpenTofu, or Helm with input schemas, artifact contracts, and operational policies.

**Capabilities:**
- **Interactive development**: Full deploy loop with compliance remediation
- **Upgrade testing**: Validate version upgrades against production configs
- **Safety guardrails**: Blocks non-development publishes and production modifications
- **Compliance automation**: Iterates until Checkov findings are resolved
- **GraphQL integration**: Full API access for complex operations

## When It Activates

The plugin auto-activates when:
- Working in `bundles/`, `artifact-definitions/`, or `platforms/` directories
- Editing `massdriver.yaml` files
- Asking about bundles, artifacts, connections, or Massdriver patterns
- Requesting to create, develop, or test bundles

## Plugin Contents

```
claude-plugins/
├── .claude-plugin/
│   └── marketplace.json
└── massdriver/
    ├── .claude-plugin/
    │   └── plugin.json
    ├── agents/
    │   ├── bundle-dev.md           # Full development workflow
    │   └── upgrade-tester.md       # Day 2 upgrade testing
    ├── commands/
    │   ├── develop.md              # /massdriver:develop
    │   ├── test-upgrade.md         # /massdriver:test-upgrade
    │   └── gen.md                  # /massdriver:gen
    ├── hooks/
    │   └── hooks.json              # Safety guardrails
    ├── templates/
    │   └── massdriver.local.md     # Settings template
    └── skills/
        └── massdriver/
            ├── SKILL.md            # Core knowledge
            ├── PATTERNS.md         # Bundle and artifact examples
            └── references/
                ├── graphql.md      # GraphQL API operations
                ├── alarms.md       # AWS/GCP/Azure monitoring
                └── compliance.md   # Checkov remediation
```

## Safety Guardrails

The plugin includes automatic safety hooks that **hard block**:

- `mass bundle publish` without `--development` flag
- Any commands targeting production environments (based on your `production_pattern`)

Read-only operations (viewing logs, artifacts) are always allowed.

## Configuration

Create `.claude/massdriver.local.md` in your project:

```yaml
---
mass_profile: default
production_pattern: (prod|production)
organization_id: ""
default_test_project: ""
---
```

| Setting | Description |
|---------|-------------|
| `mass_profile` | CLI profile from `~/.massdriver/config.yaml` |
| `production_pattern` | Regex to identify production environments (protected) |
| `organization_id` | Default org ID (optional) |
| `default_test_project` | Where to create test environments (optional) |

See `massdriver/templates/massdriver.local.md` for full documentation.

## Examples

### Creating a PostgreSQL Bundle

```
/massdriver:develop PostgreSQL database bundle.
Presets: Development (t3.small, 20GB), Staging (t3.medium, 50GB), Production (r6g.large, 100GB, Multi-AZ).
PITR should be configurable, defaulting to enabled.
SSL enforcement is mandatory.
Needs a network connection and AWS credentials.
Produces a postgres artifact with connection info.
```

### Creating an S3 Bundle

```
/massdriver:develop S3 bucket for static asset storage.
Developers choose versioning (on/off) and lifecycle policy (30/90/365 days).
Encryption at rest is non-negotiable.
Public access blocked by default but configurable.
Needs AWS credentials, produces an S3 bucket artifact.
```

### Testing an Upgrade

```
/massdriver:test-upgrade api-prod-database 1.3.0
```

The agent will ask about your production naming convention, what to copy from prod (secrets, env defaults), and whether to use exact config or low-scale equivalents for dependencies.

## Requirements

- [Massdriver CLI](https://docs.massdriver.cloud/cli/overview) (`mass`)
- OpenTofu or Terraform
- A Massdriver account with CLI credentials configured

## Learn More

- [Massdriver Documentation](https://docs.massdriver.cloud/)
- [Bundle Development Guide](https://docs.massdriver.cloud/bundles)
- [Open Source Bundles](https://github.com/massdriver-cloud/aws-rds-postgres) (examples)

## License

Apache 2.0 - See [LICENSE](LICENSE)
