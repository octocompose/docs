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
    executes: host # One of `host` and `oci`
    client:
      - os: linux
        arch: amd64
        url: https://github.com/octocompose/operator-baremetal/releases/download/v0.0.1/operator-baremetal-client-linux-amd64
        sha256Url: https://github.com/octocompose/operator-baremetal/releases/download/v0.0.1/operator-baremetal-client-linux-amd64.sha256
        binary: operator-baremetal-client
    server:
      - os: linux
        arch: amd64
        url: https://github.com/octocompose/operator-baremetal/releases/download/v0.0.1/operator-baremetal-server-linux-amd64
        sha256Url: https://github.com/octocompose/operator-baremetal/releases/download/v0.0.1/operator-baremetal-server-linux-amd64.sha256
        binary: operator-baremetal-server
```

### For Tools

```yaml
tool:
  health:
    check-tcp:
      host:
        - os: linux
          arch: amd64
          url: https://github.com/octocompose/plugins/releases/download/v0.0.1/check-tcp-linux-amd64
          sha256Url: https://github.com/octocompose/plugins/releases/download/v0.0.1/check-tcp-linux-amd64.sha256
          # Binary inside the archive.
          binary: check-tcp
        - os: linux
          arch: arm64
          url: https://github.com/octocompose/plugins/releases/download/v0.0.1/check-tcp-linux-arm64
          sha256Url: https://github.com/octocompose/plugins/releases/download/v0.0.1/check-tcp-linux-arm64.sha256
          # Binary inside the archive.
          binary: check-tcp
      oci:
        - registry: docker.io
          image: octocompose/plugin-health-check-tcp
          tag: v0.0.1
          cmd: /usr/local/bin/check-tcp
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
    host:
      - os: linux
        arch: amd64
        url: https://github.com/yourproject/yourproject/releases/download/v2.0.0/yourproject-nats-2.0.0-linux-amd64
        sha256Url: https://github.com/yourproject/yourproject/releases/download/v2.0.0/yourproject-nats-2.0.0-linux-amd64.sha256
        # Binary inside the archive.
        binary: yourproject-nats
      - os: linux
        arch: arm64
        url: https://github.com/yourproject/yourproject/releases/download/v2.0.0/yourproject-nats-2.0.0-linux-arm64
        sha256Url: https://github.com/yourproject/yourproject/releases/download/v2.0.0/yourproject-nats-2.0.0-linux-arm64.sha256
        # Binary inside the archive.
        binary: yourproject-nats
    oci:
      - registry: docker.io
        image: yourprojecters/yourproject-nats
        tag: v2.0.0
    source:
      url: https://github.com/yourproject/yourproject.git
      branch: v2.0.0
      buildCmds:
        - GOOS={{OS}} GOARCH={{ARCH}} make yourproject-nats
      binaryPath: dist/nats/yourproject-nats-{{OS}}-{{ARCH}}
```