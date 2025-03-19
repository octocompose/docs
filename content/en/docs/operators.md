---
title: Operators
linkTitle: Operators
weight: 5
description: >
  Details on different operators (baremetal, Docker, Kubernetes).
---

OctoCompose uses different operators to execute plugins and manage services in various environments. The operator is the component that understands the specifics of each environment and translates the abstract OctoCompose configuration into concrete actions.

## Available Operators

OctoCompose includes three primary operators, each designed for a specific environment:

### Baremetal Operator

The baremetal operator manages services directly on the host system.

**Features:**
- Executes binaries directly on the host system
- Manages system services through init systems (systemd, etc.)
- Performs direct TCP and service health checks through plugins
- Handles local file access for configuration and secrets
- Downloads and installs required binaries
- Manages runtime directories and permissions

**Configuration Example:**
```yaml
octoctl:
  operator: baremetal
```

### Docker Compose Operator

The Docker Compose operator manages services as Docker Compose containers.

**Features:**
- Pulls and runs Docker images
- Creates networks for service communication
- Maps ports between containers and host
- Manages volumes for persistent storage
- Handles container lifecycle (create, start, stop, remove)
- Captures container logs

**Configuration Example:**
```yaml
octoctl:
  operator: docker-compose
  network: demoproject-instance1
  volume_path: /var/OctoCompose/volumes
```

### Kubernetes Operator

The Kubernetes operator deploys and manages services in Kubernetes clusters.

**Features:**
- Creates and manages Kubernetes resources (Deployments, Services, etc.)
- Handles Kubernetes-specific configuration
- Integrates with Kubernetes health checks and probes
- Manages namespaces and resource quotas
- Supports Kubernetes-native secret management
- Captures logs from Kubernetes pods

**Configuration Example:**
```yaml
octoctl:
  operator: kubernetes
  name: instance1
  namespace: demoproject
  api: https://rancher.example.com/k8s/clusters/local
  token: <token>
```

## Common Operator Features

All operators provide these common features:

- Plugin execution and output parsing
- Configuration merging
- Service dependency management
- Version tracking
- Upgrade coordination
- Environment variable management
- Secret injection
- Logging and notification

## Operator Selection

The operator is selected in the OctoCompose configuration:

```yaml
octoctl:
  operator: baremetal
```

OctoCompose will use the specified operator for executing all commands and managing services.

## Custom Operators

OctoCompose's modular architecture allows for custom operators to be developed for specific environments or requirements. Custom operators need to implement the operator interface defined by OctoCompose.

To use a custom operator, specify its name in the configuration:

```yaml
octoctl:
  operator: my-custom-operator
  # Operator-specific configuration goes here
```

## Operator Communication

Operators communicate with services in various ways:

- **Baremetal**: Direct process management, signals, and file system
- **Docker**: Docker API and container runtime
- **Kubernetes**: Kubernetes API server

The common communication pattern is:

1. OctoCompose creates and configures the appropriate operator
2. The operator executes plugins with the merged configuration
3. Plugins communicate back to the operator through exit codes and standard output
4. The operator interprets plugin output and takes appropriate actions
5. Services receive configuration, environment variables, and secret values from the operator
