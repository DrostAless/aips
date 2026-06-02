# Agent Internet Protocol Suite

**AIPS** is a draft protocol suite for an Agent-native Internet.

AIPS defines a structured interface that allows AI agents to discover services, understand capabilities, request tasks, handle permissions, require human confirmation, and exchange results without relying on human-oriented web UI automation.

> 当前互联网主要面向人类 UI。AIPS 的目标是在现有互联网之上，为 AI Agent 提供原生、结构化、可验证的服务入口。

## Core Idea

A service can expose an Agent interface alongside its human interface:

- Human Interface: websites, apps, buttons, forms, visual UI
- Agent Interface: manifest, capability schema, task endpoint, permission policy

The minimum useful AIPS implementation contains:

```txt
manifest + capability + task + permission + confirmation
```

## Repository Structure

```txt
AIPS.md                         # Main protocol draft
examples/well-known-aips.json   # Example /.well-known/aips.json manifest
schemas/manifest.schema.json    # Draft JSON Schema for AIPS manifest
```

## Current Status

This repository is currently at **v0.1 draft** stage.

The goal is to first define a lightweight, implementable protocol before building a larger ecosystem.

## Design Goals

- Service discovery for agents
- Machine-readable capability declaration
- Standard task request and response format
- Explicit permission and risk model
- Human confirmation for high-risk actions
- Low-cost adoption for individual developers and small services
- Compatibility with existing HTTP infrastructure

## Non-goals

AIPS does not try to replace TCP/IP, HTTP, REST, GraphQL, gRPC, DNS, or existing human-facing websites.

## Specification

See [AIPS.md](./AIPS.md).
