---
title: Monitoring and Secrets Management
linkTitle: Monitoring and Secrets Management
weight: 4
description: >
  From robust security to continuous availability, OctoCompose safeguards your applications' integrity across every environment.
---

{{% pageinfo %}}
This text has been generated using a Chat bot. We will update it soon.
{{% /pageinfo %}}

OctoCompose's health checking system provides robust monitoring of your services through multiple protocols (TCP, HTTP, gRPC) with configurable intervals, success thresholds, and failure thresholds. You'll be able to define specific endpoints for health checks and customize the monitoring frequency based on each service's requirements. When health issues are detected, the integrated notification system will send alerts through configured channels like SMTP, allowing administrators to respond quickly to problems before users are significantly impacted.

For production deployments, OctoCompose's secrets management ensures that sensitive information is handled securely. By integrating with providers like `HashiCorp Vault`, you can ensure credentials and other sensitive data are securely stored and only accessed by authorized services as needed. This separation of secrets from configuration files follows security best practices and helps protect your system from unauthorized access.

The preflight validation allows users to verify their configuration before deployment, reducing the risk of failed deployments due to misconfiguration. As a developer, you can implement validation for your services to check for required configuration values, proper formatting, and valid dependencies, giving users confidence in their deployment setup.