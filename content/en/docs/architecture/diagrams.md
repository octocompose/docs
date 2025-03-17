---
title: Diagrams
weight: 99
description: >
  Diagram for OctoCompose.
---

### The execution flow Diagram

Created with [ChatGPT](https://chatgpt.com/g/g-5QhhdsfDj-presentation-diagram-generator-by-showme)

```
I have a tool called octoctl, it's a launcher that downloads an operator-client which then downloads and executes an operator.
There will be different operators baremetal, docker and kubernetes are provided but there can be more.
After the operator runs it will run a set of configured services. Take nats, idp, auth-service, webdav and my-supi as example.
```

### The config merge Diagram

Created with [mermaidchart.com](https://mermaidchart.com)

```
flowchart TD
  subgraph "Config Sources"
    LocalConfig["Local config.yaml 📄 (User-provided)"]
    RemoteConfig["Remote Configuration Files 🌐"]
    CoreConfig["Core Config (URL Repositories) 🌍"]
    ExtensionConfig["Extension Config (e.g., collabora.yaml) 🛠️"]
    VersionTracking["Version Tracking (e.g. GitHub) 🏷️"]
  end

  subgraph "Merging Process"
    Download["Download & Process Includes ⬇️"]
    CacheIncludes["Cache Includes"]
    Template["Apply Templating (?template=true) 🏗️"]
    BaseConfig["Base Config (Remote) 🏗️"]
    Override["User-Specific Overrides (Local) 🎛️"]
  end


  subgraph "Resulting Merged Configuration"
    MergedConfig["Merged Config ⚙️"]

    subgraph "Apply Globals #127760;"
        Globals["Load globals (noGlobals=False)"]
        Locals["Overwrite with service specific config"]
    end

    OperatorConfig["Operator Configuration 🎛️"]
    ServiceSpecificTemplate["Apply Templating (?template=true) 🏗️"]
    ServiceSpecific["Service Configurations 🏗️"]
  end

  LocalConfig -->|Includes| RemoteConfig
  RemoteConfig --> CoreConfig
  RemoteConfig --> ExtensionConfig
  CoreConfig --> VersionTracking
  ExtensionConfig --> VersionTracking

  Globals -->|Merge| Locals
  
  LocalConfig --> Download
  RemoteConfig --> Download
  Download --> CacheIncludes
  Download --> Template
  Template --> BaseConfig
  BaseConfig -->|Merge| Override
  Override --> MergedConfig
  MergedConfig --> Globals
  MergedConfig --> OperatorConfig
  Locals --> ServiceSpecificTemplate
  ServiceSpecificTemplate --> ServiceSpecific
```

### The plugin architecture Diagram

Edited with Draw.io - file [plugin-architecture.drawio](/docs/architecture/plugin-architecture.drawio)

```
flowchart TD
  %% Define main components
  Octoctl["octoctl 🐙"]
  OperatorClient{"Operator Client"}
  Operator{"Operator"}

  %% Define plugin categories
  subgraph "Health check Plugins 📡"
    operator_health_check
    check_tcp["check-tcp"]
    check_grpc["check-grpc"]
    check_server_url["check-server-url"]
  end

  subgraph "Preflight check Plugins ⚙️"
    operator_preflight_check
    check_tcp_port["check-tcp-port"]    
    operator_run["operator-run"]
  end

  subgraph "Notification Plugins 📢"
    operator_notifications
    smtp["smtp"]
    slack["slack"]
    signal["signal"]
    matrix["matrix"]
    telegram["telegram"]
    discord["discord"]
  end

  subgraph "Secret Manager Plugins 🔒"
    octoctl_secrets_manager
    vault["vault"]
  end

  subgraph "Managed Services 🛠️"
    operator_services
    nats["nats"]
    webdav["webdav"]
    auth_service["auth-service"]
    my_supi["my-supi"]
  end

  Octoctl -->|"Creates"| OperatorClient
  OperatorClient -->|"Creates"| Operator
  Octoctl -->|"Queries"| octoctl_secrets_manager

  %% Plugins
  operator_health_check --> check_tcp & check_grpc & check_server_url
  operator_preflight_check --> check_tcp_port & operator_run
  operator_notifications --> smtp & slack & signal & matrix & telegram & discord
  octoctl_secrets_manager --> vault

  %% Services
  operator_services --> nats & webdav & auth_service & my_supi
  

  %% Operator Connections
  Operator -->|"Executes and Parses Output"| operator_health_check
  Operator -->|"Executes and Parses Output"| operator_preflight_check
  Operator -->|"Executes and Parses Output"| operator_notifications
  Operator -->|"Executes"| operator_services
```