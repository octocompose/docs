---
title: Repository
weight: 2
description: >
  Configuration system and examples.
---

This defines where to get binaries/sources/docker containers and howto run them.


```yaml
# We allow includes in repos.
include:
    # This example might have some tools which yourproject v2.0.0 needs.
    # Full URL will be generated from the parent path: https://raw.githubusercontent.com/yourproject/octocompose-chart/refs/tags/v2.0.0/repo/
    # This allows to use the same repo for Development and Production (file:// vs. https://)
    # ?template=true means that the config will be templated by text/template with a yet to be defined set of variables.
  - url: ./tools/yourproject.yaml?template=true
    gpg: ./tools/yourproject.yaml.asc
  
    # This example will add the service abc.
  - url: ./services/abc.yaml?template=true
    gpg: ./services/abc.yaml.asc

service:
  # Each service has it's own entry nats is just a template for many of them.
  nats:
    binary:
      - os: linux
        arch: amd64
        url: https://github.com/yourproject/yourproject/releases/download/v2.0.0/yourproject-nats-2.0.0-linux-amd64
        sha256URL: https://github.com/yourproject/yourproject/releases/download/v2.0.0/yourproject-nats-2.0.0-linux-amd64.sha256
        archiveBinaryPath: yourproject-nats
        args: ["server"]
      - os: linux
        arch: arm64
        url: https://github.com/yourproject/yourproject/releases/download/v2.0.0/yourproject-nats-2.0.0-linux-arm64
        sha256URL: https://github.com/yourproject/yourproject/releases/download/v2.0.0/yourproject-nats-2.0.0-linux-arm64.sha256
        archiveBinaryPath: yourproject-nats
        args: ["server"]
    oci:
      - arch: amd64
        registry: docker.io
        image: yourprojecters/yourproject-nats
        tag: v2.0.0
    source:
      url: https://github.com/yourproject/yourproject.git
      branch: v2.0.0
      buildCmds:
        - OS={{env.GOOS}} ARCH={{env.GOARCH}} make yourproject-nats
      binaryPath: dist/nats/yourproject-nats-{{env.GOOS}}-{{env.GOARCH}}
```