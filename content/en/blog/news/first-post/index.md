---
date: 2025-03-14
title: Introducing OctoCompose - Simplified Microservice Orchestration
linkTitle: Introducing OctoCompose
description: >
  OctoCompose is a lightweight yet powerful tool for microservice orchestration that works across baremetal, Docker, and Kubernetes environments.
author: René Jochum ([@jochumdev](https://github.com/jochumdev))
resources:
  - src: "**.{png,jpg}"
    title: "Image #:counter"
    params:
      byline: "Photo: "
---

Orchestrating microservices across different environments has always been challenging. Today, we're excited to introduce the **CONCEPT OctoCompose**, a unified solution to this problem.

OctoCompose combines the flexibility of configuration-as-code with the simplicity of a declarative approach. Like its namesake, it uses multiple "tentacles" (plugins) to handle different aspects of service management - from dependency resolution to health checks and scaling.

### Key Features

- **Environment Agnostic**: Works seamlessly across baremetal, Docker, and Kubernetes
- **Smart Dependencies**: Automatically determines the correct startup sequence
- **Unified Configuration**: One configuration format for all deployment targets
- **Plugin Architecture**: Extensible through a simple plugin system

We built OctoCompose because existing tools were either too complex or too limited. It fills the gap between basic scripting and heavyweight orchestration platforms.

We're releasing this as an open-source project and look forward to community contributions. Follow our Matrix space for updates and discussions.
