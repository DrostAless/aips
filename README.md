# Agent Internet Protocol Suite

**AIPS** is a draft protocol-suite profile for an **Agent-first Internet**.

The central claim of this project is that the current Internet is usable by agents, but it is not designed correctly for agents. Human websites expose pages, layouts, buttons, forms, banners, sessions, and visual flows. Agents need something different: discoverable capabilities, explicit permissions, stable protocol references, risk metadata, confirmation policy, and audit trails.

AIPS does **not** replace A2A, MCP, AP2, HTTP, OAuth, OpenAPI, or existing human-facing websites. Its purpose is to compose existing agent protocols into a coherent Internet-level architecture for discovery, capability indexing, permission policy, confirmation, audit, registry, and governance.

> AIPS is an architectural profile for the Agent-first Internet, not a competing Agent-to-Agent wire protocol.

## Why Agent-first?

The human web makes agents behave like impaired humans: read pixels, infer layout, click buttons, close popups, survive redesigns, and guess which actions are safe. That should be a fallback, not the native interface.

An Agent-first Internet should expose intent and capability directly:

```txt
What can this service do?
Which protocol backs the action?
What permissions are required?
What risks are involved?
When must the user confirm?
What will be logged?
What will it cost?
```

## Positioning

```txt
AIPS = Agent-first Internet protocol suite / architectural profile

A2A  = Agent-to-Agent communication and task collaboration
MCP  = Agent-to-tool, resource, prompt, and context access
AP2  = Agent payment authorization and accountable commerce
HTTP = Transport and web compatibility layer
OAuth / mTLS / signatures = Authentication and authorization building blocks
```

AIPS should reuse these protocols where they already define stable semantics.

## Core Idea

A service can expose an Agent interface alongside its human interface:

- Human Interface: websites, apps, buttons, forms, visual UI
- Agent Interface: A2A Agent Card, MCP server metadata, AP2 payment flow, and optional AIPS profile metadata

The minimum useful AIPS-compatible service profile contains:

```txt
protocol references + capability profile + permission policy + confirmation policy + audit metadata
```

## Repository Structure

```txt
AIPS.md                         # Main AIPS architecture draft
README.md                       # Project overview
examples/well-known-aips.json   # Example optional AIPS service profile
schemas/manifest.schema.json    # Draft JSON Schema for the AIPS profile document
```

## Current Status

This repository is currently at **v0.3 draft** stage.

This revision adds the Agent-first Internet thesis before the protocol specification and keeps AIPS aligned with existing protocols, especially A2A, MCP, and AP2.

## Design Goals

- Explain why the human web is structurally wrong for agents
- Define principles for an Agent-native Internet
- Compose existing agent protocols instead of replacing them
- Make A2A, MCP, AP2, identity, permission, registry, and policy work together
- Define an AIPS-compatible service profile
- Provide a common capability index across A2A skills, MCP tools/resources/prompts, and payment flows
- Add explicit risk, confirmation, audit, and governance metadata
- Keep adoption lightweight for individual developers and small services
- Maintain compatibility with existing HTTP infrastructure

## Non-goals

AIPS does not attempt to:

- Replace A2A for Agent-to-Agent communication
- Replace MCP for Agent-to-tool or context access
- Replace AP2 for agent payments
- Replace OAuth, mTLS, DID, signatures, or other identity systems
- Replace REST, OpenAPI, GraphQL, gRPC, or human-facing websites
- Define a mandatory payment or business model

## Specification

See [AIPS.md](./AIPS.md).
