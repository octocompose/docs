---
title: Configuration
linkTitle: Configuration
weight: 3
description: >
  Configuration system and examples.
---

OctoCompose uses a powerful configuration system that shares config with the app and services, meaning there is a single configuration for the whole stack.

## Configuration System Overview

The configuration system in OctoCompose allows:

- Merging configurations from multiple sources (local files, URLs)
  - When using URLs, you can use `gpg` to let the octoctl verify the signature of the config and it's includes.
  - With remote URLs OctoCompose will refuse to run with an error if the remote provides no gpg signature for that config file.
- Layering configurations (core settings with environment-specific overrides)
- Templating for dynamic configurations
- Version tracking for configuration sources
- Validation through preflight checks

This approach eliminates duplicate settings and separates core settings from customizations, making it easier to manage complex deployments.

## Example `core` config

Here is an example config which you might host at:
`https://raw.githubusercontent.com/yourproject/octocompose-chart/refs/tags/v2.0.0/config/core.yaml`

```yaml
# Globals are applied to each service, individual services can overwrite them.
globals:
  client:
    preferredTransports: 
      - orbdrpc # orb uses it's own wire Format for results on top of DRPC to deliver responses with metadata.
      - grpc
  server:
    orbdrpc:
      # Templating happens at the service level.
      listen: unix://{{env.XDG_RUNTIME_DIR}}/{{project.Name}}/{{project.ID}}/{{service.App.Name}}-drpc.sock
    grpc:
      listen: unix://{{env.XDG_RUNTIME_DIR}}/{{project.Name}}/{{project.ID}}/{{service.App.Name}}-grpc.sock
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

octoctl:
  operator: baremetal # One of `baremetal`, `docker`, `kubernetes` or you provide your own.
  project: yourproject # Path for the cache
  version: v2.0.0 # Version for the cache path

  # Prompts will be asked before the operator starts or before preflight checks.
  # Having prompts will disable all autostart features.
  prompts: 
    - name: "Enter the password for vault"
      type: password
      var: VAULT_PASSWORD

  secrets:
    # Secrets will be fetched per service from the given provider and supplied into the merged config
    # to the service, here we have HashiCorp Vault as provider.
    tool: vault-client
    depends:
      - service: vault
    external: false

operator:
  repos:
    core:
      url: https://raw.githubusercontent.com/yourproject/octocompose-chart/refs/tags/v2.0.0/repos/core.yaml
      # Will be auto-detected by adding '.asc' to the url.
      # gpg: https://raw.githubusercontent.com/yourproject/octocompose-chart/refs/tags/v2.0.0/repos/core.yaml.asc
  services:
    vault:
      config:
        # This will disable the global config for this service.
        noGlobals: true
        # This will disable templating for this service.
        noTemplate: true
    nats:
      config:
        # This will disable the global config for this service.
        noGlobals: true
        # This will disable templating for this service.
        noTemplate: true
      healthCheck:
        - tool: check-tcp # will check if a connection to that port is possible.
          port: {{service.nats.port}} # This is the 9233 defined below or 4222 in the users config.
          interval: 10s
          successTreshold: 1
          failureTreshold: 3
      preflightCheck:
        - tool: check-tcp-port  # will check for an open port, it will only run in `baremetal` envs.
          port: {{service.nats.port}}
        - tool: operator-run # tool `operator-run` will run the service trough the operator with the given arguments.
          args: ["preflight"]
      hooks:
        preStart:
          - tool: operator-run
            args: ["migrate"] 
    idp: {}
    auth-service:
      depends:
        - service: idp
        - service: nats
    webdav: 
      healthCheck:
        - tool: check-grpc
          url: {{service.webdav.server.grpc.listen}}           
          endpoint: /health.v1alpha.Health/Healthz
          interval: 10s
          successTreshold: 1
          failureTreshold: 3
      preflightCheck:
        - tool: check-server-url # will check if the given url is valid (TCP Port or Unix socket)
          url: {{service.webdav.server.grpc.listen}}
        - tool: operator-run # tool `operator-run` will run the service trough the operator with the given arguments.
          args: ["preflight"]
      depends:
        - service: auth-service

# Service configurations:
service:
  nats:
    port: 9233
```

### Extensions config example

And here is an extensions for `collabora`:

```yaml
operator:
  repos:
    collabora:
      url: https://raw.githubusercontent.com/yourproject/octocompose-chart/refs/tags/v2.0.0/repos/collabora.yaml
      # Will be auto-detected by adding '.asc' to the url.
      # gpg: https://raw.githubusercontent.com/yourproject/octocompose-chart/refs/tags/v2.0.0/repos/collabora.yaml.asc
  
  services:
    collabora: {}
```

## The users config.yaml

Here is an example user-provided `config.yaml`:

```yaml
project:
  name: yourproject
  # This will be generated by the octoctl if none is provided, it will be a shortUUID.
  id: 1234567890

include:
  - url: https://raw.githubusercontent.com/yourproject/octocompose-chart/refs/tags/v2.0.0/config/core.yaml
    # Will be auto-detected by adding '.asc' to the url.
    # gpg: https://raw.githubusercontent.com/yourproject/octocompose-chart/refs/tags/v2.0.0/config/core.yaml.asc
    versions:
      format: github
      # Format github will autodetect the following url.
      # url: https://api.github.com/repos/yourproject/octocompose-chart/releases/latest
  - url: https://raw.githubusercontent.com/yourproject/octocompose-chart/refs/tags/v2.0.0/config/collabora.yaml
    # Will be auto-detected by adding '.asc' to the url.
    # gpg: https://raw.githubusercontent.com/yourproject/octocompose-chart/refs/tags/v2.0.0/config/collabora.yaml.asc
    versions:
      format: github
      # Format github will autodetect the following url.
      # url: https://api.github.com/repos/yourproject/octocompose-chart/releases/latest

# Globals are applied to each service.
globals:
  server:
    orbdrpc:
      listen: tcp://0.0.0.0:8081
    grpc:
      listen: tcp://0.0.0.0:8080
  kvstore:
    servers:
      - nats://nats:4222
  registry:
    plugin: kubedns
    namespace: yourproject
    cluster_domain: cluster.local
  # This defines helper variables for the operator.
  operator:
    ports:
      orbdrpc: 8081
      grpc: 8080

# The configuration to setup the operator, this wont get uploaded.
octoctl:
  # The kubernetes operator will run itself inside kubernetes and communicate via RPC with that "operator".
  # It will upload the merged config to a kubernetes secret, the operator then executes it.
  plugin: kubernetes 
  namespace: yourproject
  # Prefix for each resource item.
  prefix: instance1
  api: https://rancher.example.com/k8s/clusters/local
  token: <token>

operator:
  repos:
    mycustom:
      url: https://raw.githubusercontent.com/jochumdev/yourproject-plugins/refs/heads/main/repo.yaml
  
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
      enabled: false

        
  notifications:
    - notifier: smtp
      server: mail.example.com
      port: 25
      from: yourproject@example.com
      user: yourproject@example.com
      password: Abc123456
      to: yourproject-users@example.com

# Service configurations:
service:
  nats:
    port: 4222
    cluster: true

  my-supi:
    name: Alex
    number: 42
```

## Config Templates

OctoCompose supports templating in configuration files using Go's text/template syntax. This allows for dynamic configuration based on:

- Environment variables (`{{env.XDG_RUNTIME_DIR}}`)
- Project information (`{{project.Name}}`, `{{project.ID}}`)
- Service-specific values (`{{service.App.Name}}`)

Templates are processed when the `?template=true` parameter is added to included configuration URLs.
Except for repos you will not need to add the `?template=true` parameter, services get always a templated config, except they have the `operator.services.<service>.config.noTemplate` flag set.

## Version Tracking

OctoCompose can track versions of configuration sources, enabling automated version detection and updates. This is configured using the `versions` property in the include section:

```yaml
include:
  - url: https://raw.githubusercontent.com/yourproject/octocompose-chart/refs/tags/v2.0.0/config/collabora.yaml
    # Will be auto-detected by adding '.asc' to the url.
    # gpg: https://raw.githubusercontent.com/yourproject/octocompose-chart/refs/tags/v2.0.0/config/collabora.yaml.asc
    versions:
      format: github
      # Format github will autodetect the following url.
      # url: https://api.github.com/repos/yourproject/octocompose-chart/releases/latest
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
