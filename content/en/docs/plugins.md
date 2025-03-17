---
title: Plugins
linkTitle: Plugins
weight: 6
description: >
  Plugin system and available plugins.
---

OctoCompose uses a plugin-based architecture that keeps the core system lightweight while allowing for extensive functionality through plugins.

## Plugin System

The plugin system in OctoCompose follows these principles:

- **Separation of Concerns**: Each plugin has a specific responsibility
- **Standardized Interfaces**: Plugins communicate through well-defined interfaces
- **Minimal Dependencies**: Plugins have minimal dependencies on other components
- **Version Control**: Plugins are versioned independently
- **Discoverable**: Plugins can be discovered and installed through repositories

## Using Plugins

Plugins are specified in the OctoCompose configuration and are automatically downloaded and executed by the operator as needed.

### Plugin Configuration

Plugins are configured in the operator section of the configuration:

```yaml
operator:
  repos:
    core:
      url: https://github.com/octocompose/plugins/releases/download/v0.0.1/repos/core.yaml
  
  services:
    my-service:
      preflight:
        - tool: check-tcp
          args:
            port: 8080
        - tool: check-grpc
          args:
            url: grpc://localhost:9000
            endpoint: /health.V1alpha.Health/HealthZ
```

### Plugin Repositories

Plugins are distributed through repositories. The default repository is provided by the project, but you can specify custom repositories:

```yaml
operator:
  repos:
    core:
      url: https://github.com/octocompose/plugins/releases/download/v0.0.1/repos/core.yaml
    custom:
      url: https://github.com/yourproject/plugins/releases/download/v2.0.0/repos/custom.yaml
```

### Plugin Execution Flow

When OctoCompose needs to use a plugin:

1. The operator checks if the plugin is already downloaded
2. If not, it downloads the plugin from the specified repository
3. The operator executes the plugin with the provided configuration
4. The plugin performs its task and communicates back through exit codes and standard output
5. The operator interprets the plugin output and takes appropriate actions

## Creating Custom Plugins

We support custom plugins that extend the functionality:

### Plugin Interface

Plugins communicate with the operator through:

- Command-line arguments
- Standard output (stdout/stderr)
- Exit codes

### Plugin Development Guidelines

When creating plugins:

1. Focus on a single responsibility
2. Use standard interfaces for communication
3. Handle errors gracefully
4. Provide clear output for logging and debugging
5. Include a configuration schema for validation
6. Follow semantic versioning

### Plugin Distribution

Custom plugins can be distributed through:

- Repositories

## Available Plugins

The following plugins are available in the default OctoCompose repositories:

### Preflight Check Plugins

| Plugin Name | Description | Arguments |
|-------------|-------------|-----------|
| check-tcp-port | Checks for open TCP ports | port |
| run-service | Executes a service via the operator | args, env |

### Health Check Plugins

| Plugin Name | Description | Arguments |
|-------------|-------------|-----------|
| check-server-url | Validates URL accessibility | url |
| check-tcp | Checks for a connection to a TCP port | port |
| check-grpc | Checks gRPC service health | url, endpoint |
| check-orbdrpc | Checks OrbDRPC service health | url, endpoint |

### Notification Plugins

| Category    | Plugin Name | Description | Arguments |
|-------------|-------------|-------------|-----------|
| notification | smtp       | Email notifications | server, port, username, password, from, to |

### Secret Manager Plugins

| Category    | Plugin Name | Description | Arguments |
|-------------|-------------|-------------|-----------|
| secret      | vault-client | HashiCorp Vault integration | address, token/auth_method, path |
