# GraphQL API Reference

**Note**: Direct GraphQL access via CLI is not currently available. These operations must be performed via the Massdriver web UI or direct API calls with authentication.

This reference documents the GraphQL schema for understanding Massdriver's data model and what operations are available.

## CLI vs UI Operations

**Use CLI for:**
- `mass env create/list` - Environment management
- `mass pkg create/cfg/deploy/version` - Package management
- `mass bundle build/publish` - Bundle development
- `mass logs` - Deployment logs
- `mass def list/get` - Artifact definitions

**Use Web UI for:**
- Project creation
- Environment forking
- Environment description updates
- Credential/artifact configuration
- Manifest linking (connecting bundles)
- Environment defaults setup

## Environment Operations

### Create Environment

```graphql
mutation {
  createEnvironment(
    organizationId: "org-id"
    projectId: "project-id"
    name: "My Environment"
    slug: "myenv"
    description: "Environment description"
  ) {
    successful
    result {
      id
      slug
      name
    }
    messages { message }
  }
}
```

### Fork Environment

Clone an environment with optional copying of secrets, env defaults, and remote references.

```graphql
mutation {
  forkEnvironment(
    organizationId: "org-id"
    parentId: "parent-env-id"
    input: {
      name: "Forked Environment"
      slug: "forkedenv"
      description: "Forked for testing"
      copySecrets: false
      copyEnvDefaults: false
      copyRemoteReferences: false
    }
  ) {
    successful
    result {
      id
      slug
      parent { slug }
    }
    messages { message }
  }
}
```

### Update Environment

Use to set description (for journaling test results).

```graphql
mutation {
  updateEnvironment(
    organizationId: "org-id"
    id: "env-id"
    name: "My Environment"
    description: "Updated description with test results"
  ) {
    successful
    messages { message }
  }
}
```

### Decommission Environment

Decommission all packages in an environment.

```graphql
mutation {
  decommissionEnvironment(
    organizationId: "org-id"
    id: "env-id"
  ) {
    successful
    messages { message }
  }
}
```

## Package Operations

### Configure Package

```graphql
mutation {
  configurePackage(
    organizationId: "org-id"
    id: "package-slug"
    params: {
      "instance_type": "t3.micro",
      "storage_gb": 20
    }
  ) {
    successful
    result {
      slug
      params
    }
    messages { message }
  }
}
```

### Copy Package Configuration

Copy config between packages (e.g., prod to test). Fields marked `$md.copyable: false` are automatically excluded.

```graphql
mutation {
  copyPackage(
    organizationId: "org-id"
    srcPackageId: "prod-package-slug"
    dstPackageId: "test-package-slug"
    overrides: {
      "instance_type": "t3.micro",
      "replicas": 1
    }
    includeSecrets: false
  ) {
    successful
    result {
      slug
      params
    }
    messages { message }
  }
}
```

### Set Package Version

```graphql
mutation {
  setPackageVersion(
    organizationId: "org-id"
    id: "package-slug"
    version: "~1.2"
    releaseStrategy: DEVELOPMENT
  ) {
    successful
    result {
      slug
      version
      releaseStrategy
    }
    messages { message }
  }
}
```

### Deploy Package

```graphql
mutation {
  deployPackage(
    organizationId: "org-id"
    id: "package-slug"
    message: "Deployment message"
  ) {
    successful
    result {
      id
      status
      action
    }
    messages { message }
  }
}
```

### Decommission Package

```graphql
mutation {
  decommissionPackage(
    organizationId: "org-id"
    id: "package-slug"
    message: "Teardown message"
  ) {
    successful
    result {
      id
      status
    }
    messages { message }
  }
}
```

## Manifest Operations

### Create Manifest

Add a bundle to a project.

```graphql
mutation {
  createManifest(
    organizationId: "org-id"
    bundleId: "bundle-name"
    projectId: "project-id"
    name: "My Database"
    slug: "mydb"
    description: "PostgreSQL for the app"
  ) {
    successful
    result {
      id
      slug
    }
    messages { message }
  }
}
```

### Link Manifests

Connect two manifests (create connection between bundles).

```graphql
mutation {
  linkManifests(
    organizationId: "org-id"
    environmentId: "env-id"
    srcManifestId: "source-manifest-id"
    srcManifestField: "network"
    destManifestId: "dest-manifest-id"
    destManifestField: "network"
  ) {
    successful
    messages { message }
  }
}
```

## Query Operations

### Get Environment Details

```graphql
query {
  environment(organizationId: "org-id", id: "env-slug") {
    id
    slug
    name
    description
    packages {
      slug
      status
      version
      params
    }
    defaultConnections {
      label
      group
      artifact { name type }
    }
  }
}
```

### Get Project with Environments

```graphql
query {
  project(organizationId: "org-id", id: "project-slug") {
    id
    slug
    name
    environments {
      slug
      name
      packages { slug status }
    }
    manifests {
      slug
      name
      repo { name }
    }
  }
}
```

### Get Package Details

```graphql
query {
  package(organizationId: "org-id", id: "package-slug") {
    slug
    status
    version
    resolvedVersion
    deployedVersion
    releaseStrategy
    params
    paramsSchema
    bundle {
      name
      version
    }
    latestDeployment {
      id
      status
      message
    }
    connections {
      packageField
      artifact { name type }
    }
  }
}
```

### Get Deployment Logs

```graphql
query {
  deploymentLogStream(organizationId: "org-id", id: "deployment-id") {
    id
    logs {
      content
      metadata {
        step
        timestamp
      }
    }
  }
}
```

### List Bundles/Repos

```graphql
query {
  repos(organizationId: "org-id", cursor: { limit: 20 }) {
    items {
      name
      releaseChannels { name tag }
      releases(strategy: DEVELOPMENT, cursor: { limit: 5 }) {
        items {
          version
          name
          description
        }
      }
    }
    cursor { next }
  }
}
```

### Get Artifact Definitions

```graphql
query {
  artifactDefinitions(organizationId: "org-id") {
    name
    label
    schema
    ui {
      connectionOrientation
      environmentDefaultGroup
    }
  }
}
```

## Enums

### PackageStatus
- `INITIALIZED` - Created but not provisioned
- `PROVISIONED` - Successfully deployed
- `DECOMMISSIONED` - Torn down
- `FAILED` - Deployment failed
- `EXTERNAL` - External resource

### DeploymentStatus
- `PENDING` - Queued
- `RUNNING` - In progress
- `COMPLETED` - Success
- `FAILED` - Failed
- `ABORTED` - Cancelled

### ReleaseStrategy
- `STABLE` - Only stable releases
- `DEVELOPMENT` - Includes dev releases

## ID Formats

- **Package slug**: `{project}-{environment}-{manifest}` (e.g., `myapp-staging-database`)
- **Each segment**: Up to 20 chars, `a-z0-9`
- **Environment slug**: When creating, just use the slug (e.g., `agentX7K2M9`), the parent containers are automatically added

## Tips

1. **Prefer CLI** for `mass bundle publish` and `mass logs` - more reliable
2. **Use GraphQL** when you need to query multiple related entities or perform complex filtering
3. **Check `messages`** on mutation results for detailed error info
4. **Use `cursor`** for paginated queries
