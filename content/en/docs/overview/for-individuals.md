---
title: Overview for Individuals
linkTitle: Overview for Individuals
weight: 2
---

- **easy to get started**: Include configuration files from a Vendor/Project, customize/overwrite these settings in your own `config.yaml` and have a ready to use Application.
- **smart stepped upgrades**: Single command to update the application, with `smart stepping` which means it will upgrade always to the latest minor version and then to the major.
- **Preflight checks**: Validate you'r configuration before running it, have meaningfull error messages when something isn't right with the config.
- **secure secrets**: Optionaly this will integrate with systems like `HashiCorp Vault` which means there are no plaintext secrets around.

OctoCompose's configuration system lets you include and merge configuration files from different sources (local files, URLs, etc.), making it easy to manage complex deployments without duplicating settings. This means you have a base configuration for common settings and then layer on environment-specific configurations as needed.

Unlike more complex tools that require specialized knowledge, OctoCompose aims to be lightweight and easy to use, while still being powerful enough for professional deployments.
