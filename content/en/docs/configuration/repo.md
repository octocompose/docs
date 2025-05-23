---
title: Repository
weight: 2
---

This defines where to get binaries/sources/oci images.

Repos will always be templated.

## Template Variables

- `{{.projectID}}` is the project ID.
- `{{.OS}}` is the target OS (e.g. linux, windows, darwin)
- `{{.ARCH}}` is the target architecture (e.g. amd64, arm64)
- `{{.env.*}}` are the environment variables.
- `{{.configs.<service>.*}}` are the compiled (with globals) configs for a service.
- `{{.services.<service>.*}}` are the services.
- `{{.octoctl}}` is the octoctl configuration.

## Syntax

### For operators

```yaml
operator:
  baremetal:
    binary:
      linux_amd64:
        url: https://github.com/octocompose/operator-baremetal/releases/download/v0.0.1/operator-baremetal-linux-amd64
        sha256Url: https://github.com/octocompose/operator-baremetal/releases/download/v0.0.1/operator-baremetal-linux-amd64.sha256
        # Binary inside the archive, leave out if not an archive.
        binary: operator-baremetal
    source:
      # If path is set and existing, repo and ref are ignored.
      path: ../
      repo: https://github.com/octocompose/operator-baremetal.git
      ref: refs/tags/v0.0.1
      buildCmds:
        - GOOS={{.OS}} GOARCH={{.ARCH}} make
      binary: dist/{{.OS}}/{{.ARCH}}/operator-baremetal
```

### For Tools

```yaml
tool:
  check-tcp:
    baremetal:
      binary:
        linux_amd64:
          url: https://github.com/octocompose/tools/releases/download/v0.0.1/tools-linux-amd64
          sha256Url: https://github.com/octocompose/tools/releases/download/v0.0.1/tools-linux-amd64.sha256
          # Binary inside the archive, leave out if not an archive.
          binary: check-tcp
      source:
        # If path is set and existing, repo and ref are ignored.
        path: ../
        repo: https://github.com/octocompose/tools.git
        ref: refs/tags/v0.0.1
        buildCmds:
          - GOOS={{.OS}} GOARCH={{.ARCH}} make check-tcp
        binary: dist/{{.OS}}/{{.ARCH}}/check-tcp
    docker:
      registry: docker.io
      image: octocompose/tools
      tag: v0.0.1
      entrypoint: /usr/local/bin/check-tcp
      build:
        repo: https://github.com/octocompose/tools.git
        ref: refs/tags/v0.0.1
        dockerfile: Dockerfile
        context: .
```

### For Services

```yaml
# We allow includes in repos.
include:
    # This example might have some tools which demoproject v2.0.0 needs.
    # Full URL will be generated from the parent path: https://raw.githubusercontent.com/demoproject/octocompose-chart/refs/tags/v2.0.0/repos/
    # This allows to use the same repo for Development and Production (file:// vs. https://)
  - url: ./service/webdav.yaml
    gpg: ./service/webdav.yaml.asc

service:
  # Each service has it's own entry nats is just a template for many of them.
  nats:
    baremetal:
      binary:
        linux_amd64:
          url: https://github.com/demoproject/demoproject/releases/download/v2.0.0/demoproject-nats-2.0.0-linux-amd64
          sha256Url: https://github.com/demoproject/demoproject/releases/download/v2.0.0/demoproject-nats-2.0.0-linux-amd64.sha256
          # Binary inside the archive, leave out if not an archive.
          binary: demoproject-nats
      source:
        # If path is set and existing, repo and ref are ignored.
        path: ../
        repo: https://github.com/demoproject/demoproject.git
        ref: refs/tags/v2.0.0
        buildCmds:
          - GOOS={{OS}} GOARCH={{ARCH}} make demoproject-nats
        binary: dist/{{OS}}/{{ARCH}}/demoproject-nats
    docker:
      registry: docker.io
      image: demoproject/demoproject-nats
      tag: v2.0.0
      build:
        repo: https://github.com/demoproject/demoproject.git
        ref: refs/tags/v2.0.0
        dockerfile: services/nats/Dockerfile
        context: services/nats
```

# For Random files

```yaml
  files:
    opencloud_app_registry:
      url: ../../files/opencloud/app-registry.yaml
      # Template true will run text/template on it.
      template: false
    opencloud_csp:
      url: ../../files/opencloud/csp.yaml
      # Template true will run text/template on it.
      template: false
    banned_password_list:
      url: ../../files/opencloud/banned-password-list.txt
      # Template true will run text/template on it.
      template: false
```
