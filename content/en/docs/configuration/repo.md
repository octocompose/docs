---
title: Repository
weight: 2
---

This defines where to get binaries/sources/oci images.

Repos will always be templated.

## Template Variables

- `{{OS}}`: The target OS
- `{{ARCH}}`: The target architecture

## Syntax

### For operators

```yaml
operator:
  baremetal:
    binary:
      - os: linux
        arch: amd64
        url: https://github.com/octocompose/operator-baremetal/releases/download/v0.0.1/operator-baremetal-linux-amd64
        sha256Url: https://github.com/octocompose/operator-baremetal/releases/download/v0.0.1/operator-baremetal-linux-amd64.sha256
        cmd: operator-baremetal
    source:
      url: https://github.com/octocompose/operator-baremetal.git
      branch: v0.0.1
      buildCmds:
        - GOOS={{OS}} GOARCH={{ARCH}} make
      cmd: dist/{{OS}}/{{ARCH}}/operator-baremetal
```

### For Tools

```yaml
tool:
  check-tcp:
    baremetal:
      binary:
        - os: linux
          arch: amd64
          url: https://github.com/octocompose/tools/releases/download/v0.0.1/tools-linux-amd64
          sha256Url: https://github.com/octocompose/tools/releases/download/v0.0.1/tools-linux-amd64.sha256
          # Binary inside the archive.
          cmd: check-tcp
      source:
        repo: https://github.com/octocompose/tools.git
        branch: v0.0.1
        buildCmds:
          - GOOS={{OS}} GOARCH={{ARCH}} make check-tcp
        cmd: dist/{{OS}}/{{ARCH}}/check-tcp
    docker:
      registry: docker.io
      image: octocompose/tools
      tag: v0.0.1
      entrypoint: /usr/local/bin/check-tcp
      build:
        repo: https://github.com/octocompose/tools.git
        branch: v0.0.1
        dockerfile: Dockerfile
        context: .
```

### For Services

```yaml
# We allow includes in repos.
include:
    # This example might have some tools which yourproject v2.0.0 needs.
    # Full URL will be generated from the parent path: https://raw.githubusercontent.com/yourproject/octocompose-chart/refs/tags/v2.0.0/repos/
    # This allows to use the same repo for Development and Production (file:// vs. https://)
  - url: ./service/webdav.yaml
    gpg: ./service/webdav.yaml.asc

service:
  # Each service has it's own entry nats is just a template for many of them.
  nats:
    baremetal:
      binary:
        - os: linux
          arch: amd64
          url: https://github.com/yourproject/yourproject/releases/download/v2.0.0/yourproject-nats-2.0.0-linux-amd64
          sha256Url: https://github.com/yourproject/yourproject/releases/download/v2.0.0/yourproject-nats-2.0.0-linux-amd64.sha256
          # Binary inside the archive.
          cmd: yourproject-nats
      source:
        repo: https://github.com/yourproject/yourproject.git
        branch: v2.0.0
        buildCmds:
          - GOOS={{OS}} GOARCH={{ARCH}} make yourproject-nats
        cmd: dist/{{OS}}/{{ARCH}}/yourproject-nats
    docker:
      registry: docker.io
      image: yourproject/yourproject-nats
      tag: v2.0.0
      build:
        repo: https://github.com/yourproject/yourproject.git
        branch: v2.0.0
        dockerfile: services/nats/Dockerfile
        context: services/nats
```