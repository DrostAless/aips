# Agent Internet Protocol Suite

简称：AIPS  
版本：v0.2-draft  
状态：Architecture Draft  

AIPS is a protocol-suite profile for an Agent-native Internet. It is not a replacement for existing agent protocols. It defines how agent communication, tool access, service discovery, identity, permission, confirmation, audit, registry, and governance can be composed into a coherent Internet-level architecture.

---

## 1. Summary

The current Internet is primarily designed for humans. Websites, buttons, forms, layouts, popups, login flows, and visual interaction patterns are optimized for human users.

AI agents can operate on this Internet, but they often need to interpret and manipulate human interfaces. This creates instability, high cost, unclear permissions, brittle automation, and inconsistent service integration.

AIPS proposes an Agent-native architecture layer on top of the existing Internet. AIPS does not define a new replacement wire protocol for every agent interaction. Instead, it profiles and composes existing protocols.

A service may expose both interfaces:

```txt
Human Interface  -> website, app, visual UI
Agent Interface  -> A2A Agent Card, MCP server, AP2 payment flow, optional AIPS profile
```

AIPS-compatible systems should reuse existing standards where they already define the right abstraction.

---

## 2. Relationship to Existing Protocols

AIPS must avoid redefining concepts already covered by active protocols.

```txt
AIPS = Agent-native Internet protocol suite / architectural profile
A2A  = Agent-to-Agent communication, Agent Cards, skills, task collaboration
MCP  = Agent-to-tool/resource/prompt/context access
AP2  = Agent payment authorization and accountable commerce
HTTP = Transport and web compatibility
OAuth / mTLS / signatures / DID = Identity and authorization building blocks
OpenAPI / JSON Schema = API and data contract building blocks
```

### 2.1 A2A Compatibility

AIPS MUST NOT replace A2A for Agent-to-Agent communication.

When an AIPS-compatible service is itself an agent, it SHOULD expose its A2A Agent Card using the A2A discovery convention and use A2A task/message semantics for agent collaboration.

AIPS MAY reference an A2A Agent Card from an AIPS profile, but it MUST NOT redefine A2A Agent Card fields, A2A task lifecycle, A2A message semantics, or A2A artifacts.

### 2.2 MCP Compatibility

AIPS MUST NOT replace MCP for agent access to tools, resources, prompts, or contextual data.

When a service exposes tools or resources to an agent, it SHOULD expose them through MCP when MCP is appropriate. AIPS MAY index or reference MCP servers, but it MUST NOT redefine MCP tool invocation, MCP resource access, MCP prompts, or MCP transport semantics.

### 2.3 AP2 Compatibility

AIPS MUST NOT define a competing payment authorization protocol.

When an agent action involves payment, purchase, or delegated commerce, AIPS SHOULD reference an AP2-compatible payment flow or another explicitly declared payment protocol. AIPS MAY add risk, confirmation, and audit metadata around payment-related actions, but the payment authorization itself should remain in the payment protocol layer.

### 2.4 HTTP, OAuth, OpenAPI, and JSON Schema

AIPS SHOULD reuse HTTP for web transport, OAuth or equivalent mechanisms for authorization, OpenAPI where REST API description is needed, and JSON Schema where structured data validation is needed.

AIPS SHOULD NOT define new replacements for these unless a specific agent-native gap exists.

---

## 3. Motivation

Current agent access patterns include:

1. Browsing web pages;
2. Using private APIs;
3. Operating application UI;
4. Browser automation;
5. Platform-specific plugins or tools;
6. Calling agent protocols in isolation.

These approaches have several problems:

- Human UI is not designed for agents;
- DOM and CSS structures change frequently;
- Login flows, captchas, popups, and ads interfere with automation;
- APIs are inconsistent or closed;
- Agent protocols may exist but remain isolated from discovery, permission, registry, and governance layers;
- Services cannot clearly state what agents are allowed to do across protocols;
- Users cannot easily understand what an agent may do across multiple services;
- Agent permissions, risk, confirmation, and audit metadata are often implicit.

AIPS is based on the following assumption:

> Agents should not only adapt to the human Internet. The Internet should also expose native, structured, interoperable interfaces for agents.

---

## 4. Non-goals

AIPS v0.2 does not attempt to:

- Replace A2A;
- Replace MCP;
- Replace AP2;
- Replace TCP/IP, HTTP, DNS, REST, GraphQL, gRPC, OpenAPI, or JSON Schema;
- Force every website to rebuild its architecture;
- Define a specific AI model architecture;
- Bind the ecosystem to a single AI provider;
- Define a mandatory payment system;
- Require blockchain or decentralized identity;
- Solve all legal liability questions;
- Replace human-facing websites or applications.

AIPS should remain lightweight, incremental, and implementable.

---

## 5. Core Concepts

### 5.1 Agent

An Agent is a software entity that can execute tasks on behalf of a user, an organization, another agent, or its own defined objective.

### 5.2 Service

A Service is an entity that exposes capabilities to agents. A service may be a traditional web service, an MCP server, an A2A agent, a payment provider, a local device gateway, or a registry.

### 5.3 Protocol Reference

A Protocol Reference is a pointer from an AIPS profile to an existing protocol endpoint or metadata document.

Examples:

```json
{
  "protocol": "a2a",
  "version": "1.0",
  "discovery_url": "https://example.com/.well-known/agent-card.json"
}
```

```json
{
  "protocol": "mcp",
  "transport": "streamable_http",
  "server_url": "https://example.com/mcp"
}
```

### 5.4 Capability Profile

A Capability Profile is AIPS metadata that describes what a service can do across one or more underlying protocols.

AIPS capability profiles are indexes, not replacements for underlying protocol-native capability definitions.

For example:

- An A2A skill remains an A2A skill;
- An MCP tool remains an MCP tool;
- An AP2 payment flow remains an AP2 payment flow;
- AIPS may reference them and attach cross-cutting metadata such as risk, permission, confirmation, audit, policy, and registry classification.

### 5.5 AIPS Profile

An AIPS Profile is an optional document describing how a service participates in the Agent-native Internet across multiple protocols.

AIPS Profile default path:

```txt
/.well-known/aips.json
```

This path is optional and must not conflict with A2A discovery. A2A agents should still use the A2A Agent Card discovery path.

---

## 6. Logical Architecture

AIPS can be described as a composition architecture:

```txt
┌────────────────────────────────────────────┐
│ L7  Governance / Ecosystem Policy           │
│ Trust programs, registry rules, abuse policy│
├────────────────────────────────────────────┤
│ L6  Audit / Accountability / Confirmation   │
│ Logs, user approval, receipts, traceability │
├────────────────────────────────────────────┤
│ L5  Identity / Authorization                │
│ OAuth, mTLS, signatures, DID, key binding   │
├────────────────────────────────────────────┤
│ L4  Protocol Composition                    │
│ A2A, MCP, AP2, OpenAPI, custom adapters     │
├────────────────────────────────────────────┤
│ L3  Capability Profile                      │
│ Cross-protocol capability index and policy  │
├────────────────────────────────────────────┤
│ L2  Discovery / Registry                    │
│ Well-known metadata, registry, local lookup │
├────────────────────────────────────────────┤
│ L1  Transport                               │
│ HTTPS, WebSocket, localhost, IPC            │
└────────────────────────────────────────────┘
```

AIPS only defines the composition profile and cross-cutting metadata. Protocol-native execution remains in the corresponding protocol layer.

---

## 7. AIPS Profile Document

AIPS-compatible services MAY expose:

```txt
/.well-known/aips.json
```

The document should describe protocol references, service identity metadata, capability profiles, permission policy, confirmation policy, and audit policy.

Example:

```json
{
  "aips_version": "0.2",
  "profile_type": "service",
  "service": {
    "id": "com.example.agent-service",
    "name": "Example Agent Service",
    "description": "An AIPS-compatible service that exposes A2A and MCP endpoints.",
    "homepage": "https://example.com"
  },
  "protocols": [
    {
      "protocol": "a2a",
      "version": "1.0",
      "discovery_url": "https://example.com/.well-known/agent-card.json"
    },
    {
      "protocol": "mcp",
      "transport": "streamable_http",
      "server_url": "https://example.com/mcp"
    }
  ],
  "capability_profiles": [
    {
      "id": "research.summarize",
      "label": "Research and summarize a topic",
      "backing_protocol": "a2a",
      "backing_reference": "skill:research.summarize",
      "permissions": ["web.search", "content.summarize"],
      "risk_level": "medium",
      "confirmation_required": false,
      "audit_required": true
    },
    {
      "id": "file.search",
      "label": "Search user files",
      "backing_protocol": "mcp",
      "backing_reference": "tool:file.search",
      "permissions": ["files.read"],
      "risk_level": "medium",
      "confirmation_required": false,
      "audit_required": true
    }
  ],
  "policy": {
    "agent_allowed": true,
    "human_confirmation_required": [
      "payment.*",
      "email.send",
      "file.delete",
      "public.publish"
    ]
  }
}
```

---

## 8. Capability Profile Rules

Each AIPS capability profile MUST include:

- `id`
- `label`
- `backing_protocol`
- `backing_reference`
- `permissions`
- `risk_level`

Each AIPS capability profile SHOULD include:

- `confirmation_required`
- `audit_required`
- `cost_profile`
- `data_classes`
- `user_visible_summary`
- `registry_categories`

AIPS capability profiles MUST NOT redefine protocol-native schemas when a backing protocol already defines them. Instead, they should point to the protocol-native definition.

Examples:

```txt
backing_protocol = a2a, backing_reference = skill:research.summarize
backing_protocol = mcp, backing_reference = tool:file.search
backing_protocol = ap2, backing_reference = payment_flow:purchase.confirmed
backing_protocol = openapi, backing_reference = operationId:searchFiles
```

---

## 9. Task and Execution Semantics

The original AIPS draft defined its own task request and response format. This creates unnecessary overlap with A2A and MCP.

AIPS v0.2 changes this rule:

- Agent-to-Agent tasks SHOULD use A2A task and message semantics;
- Tool/resource/prompt calls SHOULD use MCP semantics;
- Payment authorization SHOULD use AP2 or another declared payment protocol;
- REST API operations MAY use OpenAPI-described HTTP endpoints;
- AIPS itself only adds cross-protocol policy, registry, audit, and confirmation metadata.

AIPS MAY define a generic execution envelope only when no backing protocol applies. Such an envelope MUST be treated as an adapter profile, not as a replacement for A2A or MCP.

---

## 10. Permission Model

AIPS SHOULD follow the principle of least privilege.

Permissions should be explicit, human-readable, revocable, and scoped.

Examples:

```txt
files.read
files.write
files.delete
email.read
email.send
calendar.read
calendar.write
payment.initiate
payment.authorize
payment.charge
public.publish
user.profile.read
```

Risk levels:

```txt
low
medium
high
critical
```

Recommended handling:

| Risk Level | Example | User Confirmation |
|---|---|---|
| low | Search public data | Usually not required |
| medium | Read private files | Context-dependent |
| high | Send email or modify calendar | Recommended |
| critical | Payment, deletion, public posting, legal action | Required |

---

## 11. Human Confirmation

AIPS should not assume that an agent may execute every action autonomously.

The following actions SHOULD require user confirmation by default:

- Payment or purchase;
- File deletion;
- Sending email or messages;
- Publishing public content;
- Account setting changes;
- Third-party authorization;
- Legal, medical, or financial actions;
- Actions with irreversible external consequences.

AIPS confirmation metadata is cross-cutting. It does not replace protocol-native confirmation or authentication states.

Example:

```json
{
  "confirmation_required": true,
  "confirmation_reason": "This action will send an email to an external recipient.",
  "action_summary": "Send email to alice@example.com",
  "expires_after_seconds": 600
}
```

---

## 12. Discovery and Registry

AIPS supports several discovery mechanisms.

### 12.1 Protocol-native Discovery

AIPS-compatible systems SHOULD prefer protocol-native discovery when available.

Examples:

```txt
A2A Agent Card discovery for A2A agents
MCP server discovery or configured MCP server endpoint for MCP servers
AP2-compatible payment flow discovery for agentic payments
```

### 12.2 Optional AIPS Profile Discovery

Services MAY expose:

```txt
/.well-known/aips.json
```

This profile provides cross-protocol metadata. It should reference protocol-native documents instead of duplicating them.

### 12.3 Registry

A public, private, or local AIPS registry MAY index AIPS-compatible profiles.

Registry record example:

```json
{
  "service_id": "com.example.agent-service",
  "aips_profile_url": "https://example.com/.well-known/aips.json",
  "protocols": ["a2a", "mcp"],
  "categories": ["research", "files", "automation"],
  "trust_level": "verified"
}
```

### 12.4 Local Discovery

Local services MAY expose AIPS-compatible profiles through localhost, mDNS, Unix socket, named pipe, or OS-level permission frameworks.

---

## 13. Payment and Commerce

AIPS MUST NOT define a competing payment authorization protocol.

For agentic commerce:

- Payment flows SHOULD use AP2 or another explicitly declared payment protocol;
- AIPS MAY classify payment-related capabilities as high or critical risk;
- AIPS MAY require human confirmation for payment-related capability profiles;
- AIPS MAY require audit records, receipts, and trace identifiers;
- AIPS SHOULD separate quote, intent, authorization, charge, refund, and dispute metadata.

Example AIPS payment capability profile:

```json
{
  "id": "commerce.purchase",
  "label": "Purchase an item with user authorization",
  "backing_protocol": "ap2",
  "backing_reference": "payment_flow:purchase",
  "permissions": ["payment.authorize"],
  "risk_level": "critical",
  "confirmation_required": true,
  "audit_required": true
}
```

---

## 14. Security Principles

AIPS implementations MUST follow these principles:

1. Unknown agents should be denied by default;
2. Permissions should be minimal and explicit;
3. High-risk actions must be auditable;
4. Services must declare capability boundaries;
5. Agents must not fabricate user intent;
6. Services must not trick agents into exceeding authorization;
7. Sensitive tasks should require confirmation;
8. Important operations should have trace identifiers;
9. Authorization must be revocable;
10. Protocol-native security rules must remain authoritative.

---

## 15. Conflict Avoidance Rules

AIPS uses the following rules to avoid conflicts with existing protocols:

### Rule 1: Reference, do not replace

If A2A, MCP, AP2, OpenAPI, OAuth, or another established protocol already defines a behavior, AIPS should reference it rather than redefine it.

### Rule 2: Profile, do not fork

AIPS may add metadata around existing protocols, but it should not fork their schemas, states, or message formats.

### Rule 3: Registry metadata is not execution metadata

AIPS registries may describe what exists and how risky it is, but execution must follow the backing protocol.

### Rule 4: Confirmation is cross-cutting

AIPS confirmation policy may require user approval before an action, but it does not replace authentication, authorization, or confirmation mechanisms defined by the backing protocol.

### Rule 5: Payment authorization remains in payment protocols

AIPS may identify commerce risk, but payment mandates, authorization proofs, and payment execution should remain in AP2 or a declared payment protocol.

---

## 16. Minimal AIPS-compatible Service

A minimal AIPS-compatible service should expose at least one protocol-native agent interface, such as:

```txt
A2A Agent Card
MCP server endpoint
OpenAPI-described service endpoint
AP2-compatible payment flow
```

It may additionally expose:

```txt
/.well-known/aips.json
```

Minimal AIPS profile:

```json
{
  "aips_version": "0.2",
  "profile_type": "service",
  "service": {
    "id": "dev.example.minimal",
    "name": "Minimal AIPS-Compatible Service"
  },
  "protocols": [
    {
      "protocol": "mcp",
      "server_url": "https://example.com/mcp"
    }
  ],
  "capability_profiles": [
    {
      "id": "echo",
      "label": "Return the input message",
      "backing_protocol": "mcp",
      "backing_reference": "tool:echo",
      "permissions": [],
      "risk_level": "low",
      "confirmation_required": false,
      "audit_required": false
    }
  ]
}
```

---

## 17. Reference Implementation Roadmap

### Phase 1: Profile Validator

Implement validation for:

- `/.well-known/aips.json`;
- protocol references;
- capability profiles;
- risk and confirmation policy.

### Phase 2: Protocol Indexer

Implement an indexer that can read:

- AIPS profile metadata;
- A2A Agent Cards;
- MCP server/tool metadata where available;
- OpenAPI documents;
- AP2 payment flow metadata where available.

### Phase 3: Local Registry

Implement a local registry that can:

- Register services;
- Classify capabilities;
- Record trust metadata;
- Show user-visible risk summaries;
- Route execution to the correct backing protocol.

### Phase 4: Permission and Audit Layer

Support:

- Scoped authorization;
- User confirmation;
- Audit logs;
- Revocation;
- Trace IDs;
- Risk classification.

---

## 18. Example Interaction Flow

### 18.1 Discovery

```txt
Agent -> discover AIPS profile
Agent -> read protocol references
Agent -> fetch A2A Agent Card or MCP server metadata
Agent -> inspect capability profiles and risk policy
```

### 18.2 Execution

```txt
If backing_protocol = a2a:
  Use A2A messages and task lifecycle

If backing_protocol = mcp:
  Use MCP tool/resource/prompt semantics

If backing_protocol = ap2:
  Use AP2-compatible payment authorization flow

If backing_protocol = openapi:
  Use HTTP operation described by OpenAPI
```

### 18.3 Confirmation

```txt
Agent -> check AIPS confirmation policy
User -> approve high-risk action
Agent -> execute through backing protocol
Service -> return protocol-native result
AIPS layer -> record audit metadata
```

---

## 19. Versioning

AIPS uses semantic versioning:

```txt
0.1-draft
0.2-draft
1.0
1.1
2.0
```

Rules:

- Same major version SHOULD remain backward compatible;
- Breaking changes MUST increase the major version;
- AIPS profile MUST declare `aips_version`;
- Agents SHOULD reject unsupported major versions;
- Protocol references SHOULD declare their protocol name and version when possible.

---

## 20. Future Extensions

Possible extensions:

- AIPS Registry Protocol;
- AIPS Trust Score;
- AIPS Local Device Control Profile;
- AIPS Agent Reputation Profile;
- AIPS Multi-Agent Workflow Profile;
- AIPS Data Contract Profile;
- AIPS Sandbox Runtime Profile;
- AIPS Privacy Policy Schema;
- AIPS Personal Developer Profile.

---

## 21. Conclusion

AIPS is not about making agents better at clicking human websites, and it is not about replacing A2A, MCP, or AP2.

AIPS is about composing agent-native protocols into an Internet-level architecture.

The first useful version should focus on:

```txt
protocol references + capability profile + permission policy + confirmation policy + audit metadata
```

If these parts are implemented, an Agent-native Internet can begin to operate without fragmenting existing agent protocols.
