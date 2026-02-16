# Massdriver Skill for Claude Code

A Claude Code skill for developing [Massdriver](https://massdriver.cloud) infrastructure bundles, artifact definitions, and platform integrations.

## Installation

```bash
/plugin marketplace add massdriver-cloud/massdriver-skill
```

## What This Skill Does

- Guides bundle development with proper lifecycle scoping
- Enforces critical rules (namespace collisions, artifact matching, generated files)
- Provides patterns for connections, artifacts, alarms, and Checkov compliance
- Includes copy-paste snippets for common files

## When It Activates

The skill auto-activates when:
- Working in `bundles/`, `artifact-definitions/`, or `platforms/` directories
- Editing `massdriver.yaml` files
- Asking about bundles, artifacts, connections, or Massdriver patterns

## Skill Contents

```
massdriver-skill/
├── SKILL.md              # Core instructions
├── PATTERNS.md           # Complete bundle and artifact examples
├── references/
│   ├── alarms.md         # AWS/GCP/Azure monitoring setup
│   └── compliance.md     # Checkov remediation workflow
└── snippets/
    ├── massdriver-yaml.yaml    # Bundle config template
    ├── artifacts-tf.tf.example # Artifact resource template
    └── operator-md.md          # Runbook template
```

## Requirements

- [Massdriver CLI](https://docs.massdriver.cloud/cli/overview) (`mass`)
- OpenTofu or Terraform

## License

Apache 2.0 - See [LICENSE](LICENSE)
