---
title: Architecture
linkTitle: Architecture
weight: 3
description: >
  Technical overview and plugin architecture of OctoCompose.
---

## Technical Overview

Will be a operator/orchestration tool.

It can be used with [go-orb](https://github.com/go-orb) but it's not limited to that.

You might see it as alternative to docker compose, but it will run baremetal and in kubernetes as well.

It should be able to download binaries from various sources or `maybe` build them from source using git.

![Execution Flow](/docs/architecture/execution-flow.png)

## Modular Architecture

OctoCompose embraces the Unix Philosophy: "Do one thing and do it well." This principle is at the core of its design:

- **Plugin-Based System**: Every tool in OctoCompose is implemented as a plugin that the operator can download and execute as needed, rather than being hardcoded into the core
- **Interchangeable Components**: Even the operator itself is replaceable, allowing for custom implementations tailored to specific environments
- **Composable Design**: The entire system is built as small, focused components that work together seamlessly
- **Clear Separation of Concerns**: Each component has a specific responsibility with well-defined interfaces
- **Slim Core**: The core system remains lightweight, focusing on orchestration rather than implementation details

This modular approach makes OctoCompose extensible, maintainable, and adaptable to different use cases without becoming bloated or overly complex.

![Plugin Architecture](/docs/architecture/plugin-architecture.svg)

## Plugin Architecture

The plugin architecture of OctoCompose is designed to be modular and extensible:

### Components

- **OctoCompose Module**: Initiates the process and creates an operator
- **Operator**: Can be one of baremetal, docker, kubernetes or a custom one - executes plugins and parses their output

### Communication Flow

The communication flow in OctoCompose follows a specific pattern:

1. OctoCompose initiates and creates the appropriate operator
2. The operator executes plugins and parses their exit codes and console output
3. Plugins communicate back to the operator through their exit status and standard output
4. Configuration flows from OctoCompose through the operator to the plugins and services

## Configuration Architecture

OctoCompose shares its config with the app and the services, meaning there is a single config for the whole stack.

![Config Architecture](/docs/architecture/config-architecture.svg)

### Configuration Merging System

The configuration merging system is a key feature of OctoCompose:

1. **Config Sources**:
   - Local config.yaml (user-provided with projectID and includes)
   - Remote configuration files from URL repositories
   - Extension configs for specific services or tools
   - Service-specific configurations

2. **Merging Process**:
   - OctoCompose downloads and processes included configuration files
   - Templates are processed if the ?template=true parameter is used
   - Configurations are layered with base configs from remote sources, then user-specific overrides
   - Version tracking is maintained for configuration files

3. **Resulting Configuration**:
   - Global settings section applied to all services
   - Operator-specific configuration
   - Service-specific configurations
   - Secrets management integration
   - Notification settings
