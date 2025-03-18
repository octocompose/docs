---
title: Notes about the Diagrams
weight: 99
---

### The execution flow Diagram

Created with [ChatGPT](https://chatgpt.com/g/g-5QhhdsfDj-presentation-diagram-generator-by-showme)

```
I have a tool called octoctl, it's a launcher that downloads and executes an operator.
There will be different operators baremetal, docker and kubernetes are provided but there can be more, octoctl will download the operator from a repository.
After the operator runs it will run a set of configured services. Take nats, idp, auth-service, webdav and my-supi as example.
Please describe it as sequence diagram.
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
    BaseConfig["Base Config (Remote) 🏗️"]
    Override["User-Specific Overrides (Local) 🎛️"]
  end


  subgraph "Resulting Merged Configuration"
    MergedConfig["Merged Config ⚙️"]

    subgraph "Apply Globals #127760;"
        Globals["Load globals (config.globals=true)"]
        Locals["Overwrite with service specific config"]
    end

    OperatorConfig["Operator Configuration 🎛️"]
    ServiceSpecificTemplate["Apply Templating (config.template=true) 🏗️"]
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
  Download --> BaseConfig
  BaseConfig -->|Merge| Override
  Override --> MergedConfig
  MergedConfig --> Globals
  MergedConfig --> OperatorConfig
  Locals --> ServiceSpecificTemplate
  ServiceSpecificTemplate --> ServiceSpecific
```

### The plugin architecture Diagram

Created with [mermaidchart.com](https://mermaidchart.com)

```
flowchart TD
  %% Define main components
  Octoctl["octoctl 🐙"]
  Operator{"Operator"}

  %% Define plugin categories
  subgraph "Preflight check Plugins ⚙️"
    operator_preflight_check
    check_tcp_port["check-tcp-port"]    
    operator_run["run (args => preflight)"]
  end

  subgraph "Managed Services 🛠️"
    operator_services
    nats["nats"]
    webdav["webdav"]
    auth_service["auth-service"]
    my_supi["my-supi"]
  end

  Octoctl -->|"Creates"| Operator

  %% Plugins
  operator_preflight_check --> check_tcp_port & operator_run

  %% Services
  operator_services --> nats & webdav & auth_service & my_supi
  

  %% Operator Connections
  Operator -->|"Executes and Parses Output"| operator_preflight_check
  Operator -->|"Executes"| operator_services
```