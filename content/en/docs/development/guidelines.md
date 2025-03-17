---
title: Guidelines
weight: 1
---

## Code Quality

OctoCompose maintains high code quality standards through automated tools and consistent practices:

- **Run golangci-lint**: Before submitting any code, run `golangci-lint run` from the project root. The configuration is in `.golangci.yml`.
- **Run tests**: Before submitting any code, run `go test -v ./...` from the project root.

## Testing

- Use [stretchr/testify](https://github.com/stretchr/testify) for all tests.
- Always test edge cases and error conditions.

## Pull Requests

- Create a feature branch for your changes.
- Keep pull requests focused on a single issue or feature.
- Write a clear description of your changes.
- Ensure all tests pass.
- Address review comments promptly.

## Version Control

- Write meaningful commit messages.
- Squash multiple commits when appropriate.
- Reference issue numbers in commit messages and pull request descriptions.
