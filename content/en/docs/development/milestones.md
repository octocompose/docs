---
title: Milestones
weight: 2
---

OctoCompose development will be done through the following major milestones:

### Milestone 1: Baremetal Operator with Operator-Client

The first phase of development focuses on creating the core functionality for the baremetal operator and its operator-client:

- Core configuration system implementation
- Basic operator-client functionality
- Baremetal operator implementation 
- Basic repository system for binaries and configurations
- Configuration merging from multiple sources
- Command-line interface (octoctl) for basic operations
- Templating system for configuration files

This initial milestone explicitly does not include:
- GPG verification
- Preflight checking
- Secret management and user prompts
- Source builds
- Version tracking

### Milestone 2: GPG verification, version tracking and smart upgrades

- Adds the `octoctl trust` command
- GPG verification for configuration files
- Version tracking for the "github" format
- Smart stepped upgrades

### Milestone 3: Preflight Checks for Baremetal

The second phase builds upon the baremetal operator by adding preflight check capabilities:

- Plugin system for preflight checks
- Checks as defined in [plugins](/docs/plugins/#preflight-check-plugins)

### Milestone 4: Docker Operator

The third phase extends OctoCompose to support Docker deployments.

### Future Milestones

After completing the four primary milestones, development will focus on:

- Secret management
- Kubernetes operator
- Source builds
