---
title: Configuration
linkTitle: Configuration
weight: 3
description: >
  Configuration system and examples.
---

OctoCompose uses a powerful configuration system that shares config with the app and services, meaning there is a single configuration for the whole stack.

## Configuration System Overview

The configuration system allows:

- Merging configurations from multiple sources (local files, URLs)
  - When using URLs, you can use `gpg` to let the octoctl verify the signature of the config and it's includes.
  - With remote URLs OctoCompose will refuse to run with an error if the remote provides no gpg signature for that config file, except you provide the `--insecure` flag.
- Layering configurations (core settings with environment-specific overrides)
- Templating for dynamic configurations
- Version tracking for configuration sources
- Validation through validate checks

This approach eliminates duplicate settings and separates core settings from customizations, making it easier to manage complex deployments.

## Formats

Although these examples are in YAML, we support multiple configuration formats:

- yaml
- json
- toml

When using a different format, the file extension should be used.

Comments aren't preserved when Dumping/Comparing.

## Example `core` config

Here is an example config which you might host at:
`https://raw.githubusercontent.com/demoproject/octocompose-chart/refs/tags/v2.0.0/config/core.yaml`

```yaml
# Globals are applied to each service, individual services can overwrite them.
globals:
  youproject:
    client:
      preferredTransports: 
        - orbdrpc # orb uses it's own wire Format for results on top of DRPC to deliver responses with metadata.
        - grpc
    server:
      orbdrpc:
        # Templating happens at the service level.
        network: unix
        listen: "{{.env.XDG_RUNTIME_DIR}}/{{.octoctl.projectID}}/{{.service.Name}}-drpc.sock"
      grpc:
        network: unix
        listen: "{{.env.XDG_RUNTIME_DIR}}/{{.octoctl.projectID}}/{{.service.Name}}-grpc.sock"
    kvstore:
      # kvstore/natsjs is fully compatible with these settings 
      # to go-micro/store/natsjs which is used in opencloud.
      plugin: natsjs
      servers:
        - nats://localhost:9233
      bucketPerTable: false
      jsonKeyValues: true
      compress: false
    registry:
      # registry/micro-kvstore is a opencloud/nats-js-kv compatible registry.
      plugin: micro-kvstore
      defaultTransport: grpc
      kvstore:
        plugin: natsjs
        bucketPerTable: false
        jsonKeyValues: true
        compress: false
        servers:
          - nats://localhost:9233

# Service configurations:
configs:
  nats:
    port: 9233

octoctl:
  operator: baremetal # One of `baremetal`, `docker-compose`, `kubernetes` or you provide your own.
  repos:
    core:
      url: ../repo/core.yaml
      # Will be auto-detected by adding '.asc' to the url.
      # gpg: https://raw.githubusercontent.com/demoproject/octocompose-chart/refs/tags/v2.0.0/repo/core.yaml.asc

services:
  nats:
    octocompose:
      config:
        noTemplate: true
      health:
        - tool: check-tcp # will check if a connection to that port is possible.
          args: 
            - {{configs.nats.port}} # This is the 9233 defined below or 4222 in the example users config.
          enabled: true # Setting it to "false" will disable this health check.
          successThreshold: 1
          failureThreshold: 3
      validate:
        - tool: check-open-tcp-port  # will check for an open tcp port
          operators: [baremetal]
          args:
            - {{configs.nats.port}}
  idp:
    octocompose:
      config:
        globals: demoproject
        withServiceName: true # Will prepend the generated config with the service name, defaults to false.
  auth_service:
    octocompose:
      config:
        globals: demoproject
    depends_on:
      idp:
        condition: service_started
      nats:
        condition: service_started
  webdav: 
    depends_on:
      auth_service:
        condition: service_started
    octocompose:
      config:
        globals: demoproject
      health:
        - tool: check-grpc
          args:
            - "--network"
            - "{{.configs.webdav.server.grpc.network}}"
            - "--listen"
            - "{{.configs.webdav.server.grpc.listen}}"
            # You can leave the endpoint for the default grpc Health check empty.
            # https://grpc.io/docs/guides/health-checking/
            # https://github.com/grpc/grpc-proto/blob/master/grpc/health/v1/health.proto
            - "--endpoint"
            - /grpc.health.v1.Health/Check
            - "--plaintext"
          successThreshold: 1
          failureThreshold: 3
      validate:
        - tool: run # tool `run` will run the service trough the operator with the given arguments.
          args: ["validate"]
      init:
        - tool: run
          args: ["migrate"]
```

### Extensions config example

And here is an extensions for `collabora`:

```yaml
octoctl:
  repos:
    collabora:
      url: ../repo/collabora.yaml
      # Will be auto-detected by adding '.asc' to the url.
      # gpg: https://raw.githubusercontent.com/demoproject/octocompose-chart/refs/tags/v2.0.0/repo/collabora.yaml.asc
  
services:
  collabora: {}
```

## The users config.yaml

Here is an example user-provided `config.yaml`:

```yaml
# OctoCompose will error out if this is missing.
projectID: 1234567890

include:
  - url: https://raw.githubusercontent.com/demoproject/octocompose-chart/refs/tags/v2.0.0/config/core.yaml
    # Will be auto-detected by adding '.asc' to the url.
    # gpg: https://raw.githubusercontent.com/demoproject/octocompose-chart/refs/tags/v2.0.0/config/core.yaml.asc
    versions:
      format: github
      # Format "github" will autodetect the following url.
      # url: https://api.github.com/repos/demoproject/octocompose-chart/releases/latest
  - url: https://raw.githubusercontent.com/demoproject/octocompose-chart/refs/tags/v2.0.0/config/collabora.yaml
    # Will be auto-detected by adding '.asc' to the url.
    # gpg: https://raw.githubusercontent.com/demoproject/octocompose-chart/refs/tags/v2.0.0/config/collabora.yaml.asc
    versions:
      format: github
      # Format "github" will autodetect the following url.
      # url: https://api.github.com/repos/demoproject/octocompose-chart/releases/latest

octoctl:
  workspace: workspace
  repos:
    mycustom:
      url: https://raw.githubusercontent.com/jochumdev/demoproject-plugins/refs/heads/main/repo.yaml

# Service configurations
configs:
  nats:
    port: 4222

  my-supi:
    name: Alex
    number: 42

services:
  nats:
    replicas: 3
  webdav:
    replicas: 2
  my-supi:
    depends:
      - service: auth-service
  badservice:
    # Its possible disable services
    disabled: true
```

## Config Templates

OctoCompose supports templating in configuration files using Go's text/template syntax. This allows for dynamic configuration based on:

- Environment variables (`{{.env.XDG_RUNTIME_DIR}}`)
- Project information (`{{.octoctl.projectID}}`)
- Service-specific values (`{{.service.Name}}`)

Service configs get always a templated, except they have the `services.<service>.config.noTemplate` flag set.

## Version Tracking

OctoCompose can track versions of configuration sources, enabling automated version detection and updates. This is configured using the `versions` property in the include section:

```yaml
include:
  - url: https://raw.githubusercontent.com/demoproject/octocompose-chart/refs/tags/v2.0.0/config/collabora.yaml
    # Will be auto-detected by adding '.asc' to the url.
    # gpg: https://raw.githubusercontent.com/demoproject/octocompose-chart/refs/tags/v2.0.0/config/collabora.yaml.asc
    versions:
      format: github
      # Format github will autodetect the following url.
      # url: https://api.github.com/repos/demoproject/octocompose-chart/releases/latest
```

This allows OctoCompose to check for newer versions of the configuration and offer updates when available.

## Configuration Merging

The configuration merging process follows this order:

1. Base configurations from remote sources are loaded
2. User-specific overrides from local config.yaml are applied
3. Service-specific configurations are merged on top of global settings

This layered approach allows you to maintain core settings in a central location while easily adding customizations for specific deployments.

## Secrets Management

Sensitive information can be managed securely through providers like HashiCorp Vault:

```yaml
octoctl:
  secrets:
    tool: vault-client
    depends:
      - service: vault
    external: false
```

Secrets are fetched per service and supplied into the merged config, ensuring that sensitive data is not exposed in configuration files.

## Prompts

OctoCompose supports interactive prompts for collecting sensitive information during startup:

```yaml
octoctl:
  prompts: 
    - name: "Enter the password for vault"
      type: password
      var: VAULT_PASSWORD
```

These prompts are treated as high-value secrets and will disable autostart features.
