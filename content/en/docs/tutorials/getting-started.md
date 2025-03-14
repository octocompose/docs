---
title: "Getting Started"
linkTitle: "Getting Started"
weight: 1
description: >
  Quick start guide for setting up and using OctoCompose.
---

This tutorial will guide you through the process of setting up OctoCompose and deploying your first microservices.

## Prerequisites

- Linux, macOS, or Windows with WSL
- For Docker mode: Docker installed and running
- For Kubernetes mode: kubectl configured with access to a cluster

## Installation

Install the `octoctl` command line tool:

```bash
# For Linux/macOS (using curl)
curl -sSL https://get.octocompose.dev | sh

# For Linux/macOS (using wget)
wget -qO- https://get.octocompose.dev | sh

# For Windows (using PowerShell)
iwr -useb https://get.octocompose.dev/win | iex
```

Verify the installation:

```bash
octoctl version
```

## Configure Your Services

Edit the `config.yaml` file to include the services you want to deploy. 

For example, to add a web server and a database:

```yaml
services:
  nginx:
    image: nginx:latest
    ports:
      80: 8080
    volumes:
      - ./html:/usr/share/nginx/html
    preflight:
      - tool: check-tcp-port
        port: 8080
    
  postgres:
    image: postgres:latest
    ports:
      5432: 5432
    env:
      POSTGRES_PASSWORD: example
      POSTGRES_USER: example
      POSTGRES_DB: example
    volumes:
      - ./data:/var/lib/postgresql/data
    preflight:
      - tool: check-tcp-port
        port: 5432
```

## Start Your Services

Start all configured services:

```bash
octoctl start
```

OctoCompose will perform preflight checks, download necessary components, and start the services in the correct order.

## Check Service Status

Monitor the status of your services:

```bash
octoctl status
```

This will show you which services are running and their health status.

## View Logs

View the logs for a specific service:

```bash
octoctl logs nginx
```

Use the `--follow` flag to stream logs in real-time:

```bash
octoctl logs --follow nginx
```

## Stop Services

Stop all running services:

```bash
octoctl stop
```

Or stop a specific service:

```bash
octoctl stop nginx
```

## Advanced Configuration

### Using a Different Operator

To use Docker instead of baremetal:

```yaml
octoctl:
  operator: docker
  project: myproject
```

To use Kubernetes:

```yaml
octoctl:
  operator: kubernetes
  namespace: myproject
```

### Adding Notifications

Configure notifications for service health alerts:

```yaml
octoctl:
  notifications:
    smtp:
      server: smtp.example.com
      port: 587
      username: your-username
      password: your-password
      from: you@example.com
      to: admin@example.com
```

### Managing Secrets

Use Vault for secure secrets management:

```yaml
operator:
  secrets:
    tool: vault-client
    external: false
    config:
      address: http://localhost:8200
      token: your-token
```

## Next Steps

Now that you have a basic OctoCompose deployment, you can:

- Add more services to your configuration
- Explore the [CLI commands](../cli.md) for managing your deployment
- Learn about [configuration merging](../configuration.md) for more complex setups
- Try different [operators](../operators.md) for your target environment
- Set up [health monitoring](../plugins.md#tool-plugins) for your services

For more detailed information, check out the full documentation.
