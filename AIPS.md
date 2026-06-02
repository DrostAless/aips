# Agent Internet Protocol Suite

简称：AIPS  
版本：v0.1-draft  
状态：Concept Draft  

AIPS defines a protocol suite for an Agent-native Internet. It allows AI agents to discover services, understand capabilities, request tasks, exchange structured data, verify permissions, and complete workflows without depending on human-oriented web interfaces.

---

## 1. Summary

The current Internet is primarily designed for humans. Websites, buttons, forms, layouts, popups, advertisements, login flows, and visual interaction patterns are optimized for human users.

AI agents can operate on this Internet, but they usually need to understand and manipulate human interfaces. This creates instability, high cost, unclear permissions, brittle automation, and inconsistent service integration.

AIPS proposes an Agent-native interface layer on top of the existing Internet.

A service may expose two interfaces:

```txt
Human Interface  -> website, app, visual UI
Agent Interface  -> manifest, capabilities, task endpoint, permission policy
```

AIPS does not replace the existing web. It gives services a structured way to expose machine-readable capabilities to agents.

---

## 2. Motivation

Current agent access patterns include:

1. Browsing web pages;
2. Using private APIs;
3. Operating application UI;
4. Browser automation;
5. Platform-specific plugins or tools.

These approaches have several problems:

- Web UI is not designed for agents;
- DOM and CSS structures change frequently;
- Login flows, captchas, popups, and ads interfere with automation;
- APIs are inconsistent or closed;
- Services cannot clearly state what agents are allowed to do;
- Agents cannot reliably discover service capability, pricing, limits, or risk;
- User permission boundaries are often implicit.

AIPS is based on the following assumption:

> Agents should not only adapt to the human Internet. The Internet should also expose native interfaces for agents.

---

## 3. Non-goals

AIPS v0.1 does not attempt to:

- Replace TCP/IP, HTTP, DNS, REST, GraphQL, or gRPC;
- Force every website to rebuild its architecture;
- Define a specific AI model architecture;
- Bind the ecosystem to a single AI provider;
- Define a mandatory payment system;
- Require blockchain or decentralized identity;
- Solve all legal liability questions;
- Replace human-facing websites or applications.

AIPS should remain lightweight, incremental, and implementable.

---

## 4. Core Concepts

### 4.1 Agent

An Agent is a software entity that can execute tasks on behalf of a user, an organization, another agent, or its own defined objective.

Examples:

- Personal assistant agent;
- Enterprise automation agent;
- Search agent;
- Teaching agent;
- Programming agent;
- Data processing agent;
- Device control agent.

### 4.2 Service

A Service is an entity that exposes capabilities to agents.

Examples:

- File service;
- Search service;
- Mail service;
- Calendar service;
- Payment service;
- Database service;
- Local device service;
- Content generation service.

### 4.3 Capability

A Capability is a machine-readable unit of service functionality.

Example:

```json
{
  "name": "file.search",
  "description": "Search files by keyword and metadata.",
  "input_schema": {
    "type": "object"
  },
  "output_schema": {
    "type": "object"
  },
  "permissions": ["files.read"]
}
```

### 4.4 Task

A Task is an executable request submitted by an agent to a service.

Example:

```json
{
  "task_id": "task_123",
  "capability": "file.search",
  "input": {
    "query": "AIPS design document"
  }
}
```

### 4.5 Manifest

A Manifest is a structured document that describes a service, its endpoints, capabilities, permissions, authentication methods, policies, limits, and version compatibility.

---

## 5. Protocol Layers

AIPS can be described as six logical layers:

```txt
┌──────────────────────────────────────┐
│ L6  Governance / Policy Layer         │
│ Rules, responsibility, risk, ecosystem │
├──────────────────────────────────────┤
│ L5  Trust / Identity Layer            │
│ Identity, signatures, auth, audit      │
├──────────────────────────────────────┤
│ L4  Task / Execution Layer            │
│ Task submission, status, result        │
├──────────────────────────────────────┤
│ L3  Capability Layer                  │
│ Capability declaration and schemas     │
├──────────────────────────────────────┤
│ L2  Discovery Layer                   │
│ Service discovery and manifest access  │
├──────────────────────────────────────┤
│ L1  Transport Layer                   │
│ HTTPS, WebSocket, localhost, IPC       │
└──────────────────────────────────────┘
```

---

## 6. AIPS Manifest

A service supporting AIPS SHOULD expose a manifest at:

```txt
/.well-known/aips.json
```

Example:

```json
{
  "aips_version": "0.1",
  "service": {
    "id": "com.example.filebox",
    "name": "Example FileBox",
    "description": "An agent-accessible file storage service.",
    "homepage": "https://example.com",
    "provider": {
      "name": "Example Inc.",
      "contact": "support@example.com"
    }
  },
  "endpoints": {
    "task": "https://example.com/aips/task",
    "capabilities": "https://example.com/aips/capabilities",
    "auth": "https://example.com/aips/auth"
  },
  "capabilities": [
    {
      "name": "file.search",
      "description": "Search files by keyword.",
      "input_schema": {
        "type": "object",
        "properties": {
          "query": {
            "type": "string"
          }
        },
        "required": ["query"]
      },
      "output_schema": {
        "type": "object",
        "properties": {
          "results": {
            "type": "array"
          }
        }
      },
      "permissions": ["files.read"],
      "risk_level": "low",
      "rate_limit": {
        "requests_per_minute": 60
      },
      "cost": {
        "type": "free"
      }
    }
  ],
  "auth": {
    "methods": ["oauth2", "api_key", "session_token"]
  },
  "policy": {
    "agent_allowed": true,
    "human_confirmation_required": [
      "payment.charge",
      "email.send",
      "file.delete"
    ]
  }
}
```

---

## 7. Capability Specification

Each Capability MUST include:

- `name`
- `description`
- `input_schema`
- `output_schema`
- `permissions`

Each Capability SHOULD include:

- `rate_limit`
- `cost`
- `risk_level`
- `confirmation_policy`
- `examples`

Example:

```json
{
  "name": "email.send",
  "description": "Send an email on behalf of the authenticated user.",
  "input_schema": {
    "type": "object",
    "properties": {
      "to": {
        "type": "string"
      },
      "subject": {
        "type": "string"
      },
      "body": {
        "type": "string"
      }
    },
    "required": ["to", "subject", "body"]
  },
  "output_schema": {
    "type": "object",
    "properties": {
      "message_id": {
        "type": "string"
      },
      "status": {
        "type": "string"
      }
    }
  },
  "permissions": ["email.send"],
  "risk_level": "high",
  "confirmation_policy": {
    "human_confirmation_required": true
  }
}
```

---

## 8. Task Request

### 8.1 Request Format

```json
{
  "aips_version": "0.1",
  "agent": {
    "id": "agent.example.personal-assistant",
    "name": "Personal Assistant Agent"
  },
  "task": {
    "id": "task_001",
    "capability": "file.search",
    "input": {
      "query": "Agent Internet Protocol Suite"
    }
  },
  "context": {
    "user_intent": "Find relevant documents about AIPS.",
    "locale": "zh-CN"
  },
  "auth": {
    "type": "bearer",
    "token": "..."
  }
}
```

### 8.2 Response Format

```json
{
  "aips_version": "0.1",
  "task_id": "task_001",
  "status": "completed",
  "result": {
    "results": []
  },
  "usage": {
    "requests": 1,
    "cost": 0
  }
}
```

### 8.3 Task Status

Valid task states:

```txt
queued
running
requires_confirmation
completed
failed
cancelled
expired
```

---

## 9. Permission Model

AIPS SHOULD follow the principle of least privilege.

Permission examples:

```txt
files.read
files.write
files.delete
email.read
email.send
calendar.read
calendar.write
payment.initiate
payment.charge
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
| critical | Payment, deletion, legal action | Required |

---

## 10. Human Confirmation

AIPS should not assume that an agent may execute every action autonomously.

The following actions SHOULD require user confirmation by default:

- Payment;
- Purchase;
- File deletion;
- Sending email;
- Publishing public content;
- Account setting changes;
- Third-party authorization;
- Legal, medical, or financial consequences.

Confirmation response example:

```json
{
  "status": "requires_confirmation",
  "confirmation": {
    "reason": "This task will send an email to an external recipient.",
    "action_summary": "Send email to alice@example.com",
    "expires_at": "2026-06-02T18:00:00Z"
  }
}
```

---

## 11. Service Discovery

AIPS supports three discovery mechanisms.

### 11.1 Well-known Manifest

```txt
/.well-known/aips.json
```

### 11.2 Registry

A public or private registry MAY index AIPS-compatible services.

Registry record example:

```json
{
  "service_id": "com.example.filebox",
  "manifest_url": "https://example.com/.well-known/aips.json",
  "categories": ["file", "storage", "search"],
  "trust_level": "verified"
}
```

### 11.3 Local Discovery

Local services MAY expose AIPS capabilities through localhost, mDNS, Unix socket, named pipe, or OS-level permission frameworks.

Example:

```txt
http://localhost:47321/.well-known/aips.json
```

---

## 12. Agent-to-Agent Collaboration

AIPS can support agent-to-agent interaction. A service may itself be an agent.

Example use cases:

- A personal assistant delegates research to a research agent;
- A teaching agent delegates exam generation to an assessment agent;
- A coding agent delegates review to a security agent;
- An enterprise agent delegates report generation to a finance agent.

Example declaration:

```json
{
  "service_type": "agent",
  "capabilities": [
    {
      "name": "research.summarize",
      "description": "Research a topic and return a structured summary."
    }
  ]
}
```

---

## 13. Cost Declaration

AIPS does not require a payment system, but services MAY declare cost information.

Example:

```json
{
  "cost": {
    "type": "metered",
    "unit": "request",
    "price": 0.001,
    "currency": "USD"
  }
}
```

Supported cost types:

```txt
free
metered
subscription
quota
negotiated
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
8. Every task should have a unique ID;
9. Important operations should be traceable;
10. Authorization must be revocable.

---

## 15. Error Format

AIPS errors SHOULD use a consistent structure:

```json
{
  "aips_version": "0.1",
  "task_id": "task_001",
  "status": "failed",
  "error": {
    "code": "permission_denied",
    "message": "The agent does not have files.read permission.",
    "recoverable": true
  }
}
```

Common error codes:

```txt
invalid_request
invalid_schema
unknown_capability
permission_denied
rate_limited
confirmation_required
task_expired
service_unavailable
internal_error
```

---

## 16. Minimal Implementation

A minimal AIPS service only needs:

```txt
/.well-known/aips.json
/aips/task
```

Minimal manifest:

```json
{
  "aips_version": "0.1",
  "service": {
    "id": "dev.example.minimal",
    "name": "Minimal AIPS Service"
  },
  "endpoints": {
    "task": "https://example.com/aips/task"
  },
  "capabilities": [
    {
      "name": "echo",
      "description": "Return the input message.",
      "input_schema": {
        "type": "object",
        "properties": {
          "message": {
            "type": "string"
          }
        },
        "required": ["message"]
      },
      "output_schema": {
        "type": "object",
        "properties": {
          "message": {
            "type": "string"
          }
        }
      },
      "permissions": []
    }
  ]
}
```

---

## 17. Reference Implementation Roadmap

### Phase 1: Local Service

Implement:

```txt
localhost:8787/.well-known/aips.json
localhost:8787/aips/task
```

Initial capabilities:

```txt
echo
time.now
file.search
file.read
```

### Phase 2: Agent Client

The client should:

- Fetch manifest;
- Display capabilities;
- Validate input against schema;
- Submit task requests;
- Parse responses;
- Handle errors and confirmation.

### Phase 3: Registry

The registry should:

- Register services;
- Search services;
- Classify services;
- Validate manifests;
- Record trust metadata.

### Phase 4: Permission System

Support:

- API key;
- OAuth;
- Local authorization;
- User confirmation;
- Audit logs.

---

## 18. Example Interaction Flow

```txt
Agent -> GET https://example.com/.well-known/aips.json
Service -> manifest

Agent -> inspect capabilities
Agent -> choose file.search
Agent -> POST /aips/task
Service -> task result
```

With confirmation:

```txt
Agent -> POST /aips/task
Service -> requires_confirmation
User -> approve
Agent -> resume task
Service -> completed
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

- The same major version SHOULD remain backward compatible;
- Breaking changes MUST increase the major version;
- Manifest MUST declare `aips_version`;
- Agents SHOULD reject unsupported major versions.

---

## 20. Future Extensions

Possible extensions:

- AIPS Registry Protocol;
- AIPS Trust Score;
- AIPS Payment Declaration;
- AIPS Local Device Control;
- AIPS Agent Reputation;
- AIPS Task Negotiation;
- AIPS Multi-Agent Workflow;
- AIPS Data Contract;
- AIPS Sandbox Runtime;
- AIPS Privacy Policy Schema.

---

## 21. Conclusion

AIPS is not about making agents better at clicking human websites. It is about giving agents a native Internet interface.

The first version should focus on:

```txt
manifest + capability + task + permission + confirmation
```

If these five parts are implemented, an Agent-native Internet can begin to operate.
