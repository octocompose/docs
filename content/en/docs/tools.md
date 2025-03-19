---
title: Tools
linkTitle: Tools
weight: 6
description: >
  Tool system and available tools.
---

OctoCompose uses a tool-based architecture that keeps the core system lightweight while allowing for extensive functionality through tools.

## Tool System

The tool system in OctoCompose follows these principles:

- **Separation of Concerns**: Each tool has a specific responsibility
- **Standardized Interfaces**: Tools communicate through well-defined interfaces
- **Minimal Dependencies**: Tools have minimal dependencies on other components
- **Version Control**: Tools are versioned independently
- **Discoverable**: Tools can be discovered and installed through repositories

## Using Tools

Tools are specified in the OctoCompose configuration and are automatically downloaded and executed by the operator as needed.

### Tool Configuration

Tools are configured in the operator section of the configuration:

```yaml
operator:
  repos:
    core:
      url: https://github.com/octocompose/tools/releases/download/v0.0.1/repos/core.yaml
  
services:
  my-service:
    validate:
      - tool: check-open-tcp-port
        args:
          - "--port"
          - 8080
    health:
      - tool: check-grpc
        args:
          - "--address"
          - localhost:9000
          - "--endpoint"
          - /grpc.health.v1.Health/Check
```

### Tool Repositories

Tools are distributed through repositories. The default repository is provided by the project, but you can specify custom repositories:

```yaml
operator:
  repos:
    core:
      url: https://github.com/octocompose/tools/releases/download/v0.0.1/repos/core.yaml
    custom:
      url: https://github.com/demoproject/tools/releases/download/v2.0.0/repos/custom.yaml
```

### Tool Execution Flow

When OctoCompose needs to use a tool:

1. The operator checks if the tool is already downloaded
2. If not, it downloads the tool from the specified repository
3. The operator executes the tool with the provided configuration
4. The tool performs its task and communicates back through exit codes and standard output
5. The operator interprets the tool output and takes appropriate actions

## Creating Custom Tools

We support custom tools that extend the functionality:

### Tool Interface

Tools communicate with the operator through:

- Command-line arguments
- Standard output (stdout/stderr)
- Exit codes

### Tool Development Guidelines

When creating tools:

1. Focus on a single responsibility
2. Use standard interfaces for communication
3. Handle errors gracefully
4. Provide clear output for logging and debugging
5. Include a configuration schema for validation
6. Follow semantic versioning

### Tool Distribution

Custom tools can be distributed through:

- Repositories

## Available Tools

The following tools are available in the default OctoCompose repositories:

### Validation Tools

| Tool Name             | Description | Options |
|-----------------------|-------------|-----------|
| check-open-tcp-port   | Checks for open TCP ports | port |
| run                   | Executes a service via the operator | args |

### Health Check Tools

| Tool Name             | Description | Options |
|-----------------------|-------------|-----------|
| check-server-url      | Validates URL accessibility | network, address, endpoint, plaintext |
| check-tcp             | Checks for a connection to a TCP port | port |
| check-grpc            | Checks gRPC service health | network, address, endpoint, plaintext |
| check-orbdrpc         | Checks OrbDRPC service health | network, address, endpoint |

### Secret Manager Tools

| Tool Name             | Description | Options |
|-----------------------|-------------|-----------|
| vault-client          | HashiCorp Vault integration | address, token/auth_method, path |
