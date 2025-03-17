---
title: Command Line Interface
linkTitle: CLI
weight: 4
description: >
  Command reference and usage examples for the OctoCompose CLI.
---

The `octoctl` command line interface provides a set of commands for managing your deployments.

## Basic Usage

```bash
octoctl [command] [subcommand] [options]
```

## Commands

### octoctl trust

Manage gpg keys.

Subcommands:
- `add`: Add a new gpg key
- `remove`: Remove a trusted gpg key
- `list`: List all trusted gpg keys
- `all`: Trust all gpg keys the config requires

```bash
octoctl trust add [key-id]
octoctl trust remove [key-id]
octoctl trust list
octoctl --config config.yaml trust all --yes
```

Options:
- `--file`: Path to a gpg public key file

```bash
octoctl trust add --file [path]
```

### octoctl start

Starts all services defined in the configuration.

```bash
octoctl start [service-name]
```

If `service-name` is provided, only that service will be started.

Options:
- `--force`: Force start even if preflight checks fail
- `--no-deps`: Don't start dependencies

### octoctl stop

Stops running services.

```bash
octoctl stop [service-name]
```

If `service-name` is provided, only that service will be stopped.

Options:
- `--force`: Force stop without waiting for graceful termination

### octoctl restart

Restarts running services.

```bash
octoctl restart [service-name]
```

Options:
- `--force`: Force restart even if preflight checks fails.

### octoctl status

Shows the status of all services.

```bash
octoctl status [service-name]
```

If `service-name` is provided, only shows status for that service.

Options:
- `--watch`: Continuously watch status updates
- `--json`: Output in JSON format

### octoctl logs

Displays logs for the specified service.

```bash
octoctl logs [service-name]
```

Options:
- `--follow`: Follow log output
- `--tail=N`: Show last N lines
- `--timestamps`: Include timestamps

### octoctl check

Runs preflight checks on services.

```bash
octoctl check [service-name]
```

Options:
- `--verbose`: Detailed check output
- `--timeout=30s`: Check timeout duration

### octoctl config

Manages OctoCompose configuration.

Subcommands:
- `validate`: Validates configuration
- `show`: Shows merged configuration
- `diff`: Shows differences between configurations

```bash
octoctl config validate
octoctl config show [service-name]
octoctl config diff [old-version] [new-version]
```

### octoctl autostart

Manages service autostart settings.

```bash
octoctl autostart enable [service-name]
octoctl autostart disable [service-name]
octoctl autostart status
```

### octoctl version

Displays version information.

```bash
octoctl version
```

Options:
- `--check`: Check for updates
- `--json`: Output in JSON format

### octoctl upgrade

Upgrades OctoCompose and its services.

```bash
octoctl upgrade [version]
```

If `version` is provided, upgrades to that specific version.

Options:
- `--dry-run`: Show what would be upgraded without making changes
- `--force`: Force upgrade even if there are compatibility issues

## Common Options

These options apply to most commands:

- `--config=PATH`: Specify configuration file path
- `--verbose`: Increase output verbosity
- `--quiet`: Suppress output except errors
- `--help`: Show help for a command

## Use Cases

### Basic Service Management

```bash
# Start all services
octoctl start

# Check status of all services
octoctl status

# Restart a specific service
octoctl restart auth-service

# View logs for a service
octoctl logs auth-service --follow

# Stop all services
octoctl stop
```

### Configuration Management

```bash
# Validate current configuration
octoctl config validate

# Show merged configuration for a specific service
octoctl config show auth-service

# See differences between configuration versions
octoctl config diff v1.0.0 v2.0.0
```

### System Integration

```bash
# Enable autostart for a service
octoctl autostart enable auth-service

# Check autostart status
octoctl autostart status
```

### Upgrading

```bash
# Check for updates
octoctl version --check

# Upgrade to latest version
octoctl upgrade

# Upgrade to specific version
octoctl upgrade v2.1.0
```
