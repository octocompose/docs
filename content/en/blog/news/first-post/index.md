---
date: 2025-03-14
title: Introducing OctoCompose - Simplified Microservice Orchestration
linkTitle: Introducing OctoCompose
description: >
  A CONCEPT of a lightweight yet powerful tool for microservice orchestration that works across baremetal, Docker, and Kubernetes environments.
author: René Jochum ([@jochumdev](https://github.com/jochumdev))
resources:
  - src: "**.{png,jpg}"
    title: "Image #:counter"
    params:
      byline: "Photo: "
---

Orchestrating microservices across different environments has always been challenging. Today, we're excited to introduce the **CONCEPT** of OctoCompose, a unified solution to this problem.

OctoCompose combines the flexibility of configuration-as-code with the simplicity of a declarative approach. Like its namesake, it uses multiple "tentacles" (plugins) to handle different aspects of service management.

## Key Features

- **Environment Agnostic**: Run the same configuration in baremetal, Docker, or Kubernetes
- **Plugin Architecture**: Extensible design keeps the core lightweight
- **Powerful Configuration**: Advanced config merging from multiple sources with templating
- **Health Monitoring**: Built-in health checks with notification capabilities
- **Version Management**: Track and manage versions for smooth upgrades
- **Secret Management**: Securely handle sensitive information

## Project Status

OctoCompose is currently in concept phase. The core architecture and plugin system are being designed, with initial implementations focused on the baremetal operator.

## Why

We built OctoCompose because existing tools were either too complex or too limited. It fills the gap between basic scripting and heavyweight orchestration platforms.

We're releasing this as an open-source project and look forward to community contributions. Follow our Matrix space for updates and discussions.
