---
title: For docker-compose users
linkTitle: For docker-compose users
weight: 4
---

> Why OctoCompose Makes Your Life Easier

OctoCompose enhances your container orchestration experience by providing a powerful overlay to the familiar compose tooling you already know and love. It addresses key pain points while preserving the simplicity that makes compose files so popular.

## For Users: Beyond Basic Orchestration

If you're using compose files to run applications, OctoCompose offers several advantages:

### 🔄 Smart Configuration Management

- **Version-aware deployments**: OctoCompose intelligently handles application versions, ensuring you're always running compatible configurations.
- **Configuration inheritance**: Use base configurations from vendors and override only what you need, keeping your custom settings minimal and maintainable.

### 🛠️ Simplified Operations

- **One-command upgrades**: Update entire application stacks with a single command that handles all dependent services and migrations.
- **Preflight validation**: Catch configuration errors before deployment, not during runtime when they cause failures.
- **Consistent environments**: Ensure your staging and production environments use identical configurations with environment-specific overrides only where needed.

### 🔐 Enhanced Security

- **Secret management integration**: Connect to external secret stores like HashiCorp Vault rather than storing sensitive data in environment files.
- **Reduced configuration sprawl**: Centralize your settings to minimize security exposure and configuration drift.

## For Developers: Streamlined Distribution

If you're developing applications that use compose files for deployment, OctoCompose provides tools to make distribution and maintenance easier:

### 📦 Improved Distribution Model

- **Versioned configuration packages**: Distribute your application configurations as versioned packages that users can easily consume.
- **Component modularity**: Let users choose which components to deploy through a modular configuration system.
- **Managed upgrade paths**: Define proper upgrade sequences that ensure data migrations happen in the correct order.

### 🧩 Advanced Configuration Features

- **Template-based customization**: Use powerful templating to generate configurations based on user environments.
- **Global variables**: Define values once and use them throughout your configuration stack.
- **Conditional components**: Enable or disable features based on deployment requirements without complicated scripting.

### 👥 Enhanced User Experience

- **Simplified onboarding**: Provide users with minimal configuration requirements while handling the complexity behind the scenes.
- **Consistent deployment**: Ensure your application deploys the same way across different environments and user setups.
- **Reduced support burden**: Catch common configuration mistakes before they lead to support tickets.