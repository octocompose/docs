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
```

### For Tools

```yaml
tool:
  health:
    check-tcp:
      baremetal:
        - os: linux
          arch: amd64
          url: https://github.com/octocompose/tools/releases/download/v0.0.1/health-check-tcp-linux-amd64
          sha256Url: https://github.com/octocompose/tools/releases/download/v0.0.1/health-check-tcp-linux-amd64.sha256
          # Binary inside the archive.
          cmd: health-check-tcp
        - os: linux
          arch: arm64
          url: https://github.com/octocompose/tools/releases/download/v0.0.1/health-check-tcp-linux-arm64
          sha256Url: https://github.com/octocompose/tools/releases/download/v0.0.1/health-check-tcp-linux-arm64.sha256
          # Binary inside the archive.
          cmd: health-check-tcp
      docker:
        registry: docker.io
        image: octocompose/tools-health-check-tcp
        tag: v0.0.1
        cmd: /usr/local/bin/health-check-tcp
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
      - os: linux
        arch: amd64
        url: https://github.com/yourproject/yourproject/releases/download/v2.0.0/yourproject-nats-2.0.0-linux-amd64
        sha256Url: https://github.com/yourproject/yourproject/releases/download/v2.0.0/yourproject-nats-2.0.0-linux-amd64.sha256
        # Binary inside the archive.
        cmd: yourproject-nats
      - os: linux
        arch: arm64
        url: https://github.com/yourproject/yourproject/releases/download/v2.0.0/yourproject-nats-2.0.0-linux-arm64
        sha256Url: https://github.com/yourproject/yourproject/releases/download/v2.0.0/yourproject-nats-2.0.0-linux-arm64.sha256
        # Binary inside the archive.
        cmd: yourproject-nats
    docker:
      registry: docker.io
      image: yourproject/yourproject-nats
      tag: v2.0.0
    source:
      url: https://github.com/yourproject/yourproject.git
      branch: v2.0.0
      buildCmds:
        - GOOS={{OS}} GOARCH={{ARCH}} make yourproject-nats
      cmd: dist/nats/yourproject-nats-{{OS}}-{{ARCH}}
```