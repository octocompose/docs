---
title: Overview for Vendors/Projects
linkTitle: Overview for Vendors/Projects
weight: 3
---

If you're developing and distributing software, OctoCompose offers unique advantages:

- Ship versioned configurations that works from your Servers/Github repo.
- Let the user decide which components of your Application and plugins he wanna use, by defining individual configurations per component.
- **powerfull configuration system**: `includes`, `globals`, `templating` and `merging` are just some of the features of the config system.

OctoCompose's update system handles the complexity of multi-step upgrades for you. For example, when a user wants to update from v1.0.0 to v2.0.0, OctoCompose will first update to the latest v1.x.x version before proceeding to v2.0.0. This ensures that all necessary migrations are performed in sequence, making even major version jumps reliable for your users.

This stepwise update approach allows you to confidently deliver breaking changes and complex data migrations while ensuring a smooth experience for your users. They'll be able to trigger these complex upgrades with a single command, while your services will have the context they need to perform any required data transformations.
