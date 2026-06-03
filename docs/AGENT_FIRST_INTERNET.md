# Agent-first Internet

This document explains the philosophical and architectural starting point of AIPS.

AIPS is not only a technical profile. It begins from a larger claim: the current Internet is usable by agents, but it is not designed correctly for agents.

---

## 1. Why the Current Internet Is Wrong for Agents

The web was built around a human sitting in front of a screen.

That assumption shaped almost everything: pages, buttons, links, forms, visual hierarchy, navigation menus, advertisements, cookie banners, dashboards, checkout flows, login sessions, and attention-based product design.

AI agents break this assumption.

An agent does not need a hero section, a sidebar, a hover effect, a modal dialog, or an infinite feed. It needs to know:

```txt
What can this service do?
What inputs does it require?
What output will it return?
What authority is required?
What risks are involved?
What will it cost?
What should be logged?
When must the user approve?
Which underlying protocol should be used?
```

The current Internet can be used by agents, but it is not correct for agents. Most agent automation today treats the web as a visual puzzle: observe a page, infer intent from layout, click controls, extract results, recover from UI changes, and repeat. This is powerful as a fallback, but it should not be the primary interface for an agent-native world.

AIPS starts from a different premise:

> The Agent-first Internet should expose intent, capability, permission, risk, and accountability as protocol-level objects, not as side effects hidden behind human UI.

### 1.1 Human UI is the wrong abstraction

Most current agent workflows ask agents to operate websites the way humans do: inspect the screen, infer meaning from visual position, click buttons, fill forms, and wait for page transitions.

A human interface is optimized for perception, persuasion, brand, habit, and visual decision-making. An agent interface should be optimized for capability discovery, schema validation, permission scoping, deterministic execution, and auditability.

When an agent must use a human UI, the agent is forced to solve problems that should not exist:

- Which button is the correct one?
- Did the page load completely?
- Did a modal block the task?
- Is this banner relevant or decorative?
- Did the layout change?
- Is this text content, navigation, or advertisement?
- Is this confirmation legally meaningful or just a UI pattern?

These are artifacts of human interface design, not essential parts of the task.

### 1.2 The DOM is not a service contract

For agents, a web page is often treated as an API by accident. The DOM becomes an unstable contract even though it was never designed to be one.

DOM nodes, CSS classes, button labels, and layout structure can change without versioning. A service can redesign a page and silently break agent workflows. The agent has no stable declaration of capability, input schema, output schema, permission requirements, pricing, or risk.

An Agent-first Internet needs explicit machine-readable contracts. Presentation can change independently, but the service contract must remain discoverable, versioned, and testable.

### 1.3 Meaning is trapped inside presentation

Human websites often mix many kinds of information into the same page:

- actual content;
- navigation;
- recommendations;
- advertisements;
- legal notices;
- user interface state;
- analytics hooks;
- personalization;
- tracking and consent flows.

A human can usually distinguish these by context. An agent must infer the distinction from noisy signals.

For an agent, meaning should not be trapped in pixels or presentation. The service should declare its capabilities, data classes, actions, constraints, and consequences directly.

### 1.4 Permission is session-centric, not delegation-centric

The current web usually assumes a human logs in and acts directly inside a session. This model is weak for agents.

Agents need delegated authority:

```txt
This agent may read these files.
This agent may draft emails but not send them.
This agent may compare prices but not purchase.
This agent may book only after confirmation.
This agent may spend at most this amount.
This agent may act only until this time.
This agent must produce an audit trail.
```

Traditional login sessions do not express this well. They often give the agent the same broad authority as the user, or they force the agent to operate without clear authorization.

An Agent-first Internet must make delegated authority explicit, scoped, revocable, and understandable.

### 1.5 Risk is hidden too late in the flow

Many services do not expose machine-readable risk levels. An agent may discover that an action is dangerous only after reaching a final confirmation page or after execution has already changed external state.

An agent-native service should declare risk before execution:

```txt
low       -> read public information
medium    -> access private data
high      -> send, modify, schedule, or publish
critical  -> pay, delete, legally commit, or create irreversible external effects
```

Risk should be visible during planning, not discovered through trial and error.

### 1.6 The current web optimizes attention, not task completion

A significant part of the human web is optimized to capture attention, increase engagement, increase time on site, upsell, cross-sell, or influence a user visually.

Agents invert this priority.

An agent does not need to be persuaded. It needs to complete a task correctly, cheaply, safely, and with user-aligned intent. Interfaces built for persuasion add irrelevant incentives, distractions, and ambiguity.

An Agent-first Internet should optimize for:

- task completion;
- correctness;
- accountability;
- low latency;
- explicit cost;
- user intent preservation;
- minimal unnecessary interaction.

### 1.7 APIs alone are not enough

One might say: agents should simply use APIs. That is only partially correct.

Existing APIs are often:

- private or undocumented;
- inconsistent across services;
- not discoverable by agents;
- missing permission and risk metadata;
- missing human confirmation semantics;
- missing user-visible action summaries;
- missing audit requirements;
- not designed for cross-agent or cross-service composition.

AIPS does not reject APIs. It treats APIs, A2A, MCP, AP2, and OpenAPI as components. But an Agent-first Internet needs a higher-level profile that explains how these pieces fit together.

### 1.8 There is no shared accountability layer

When an agent acts across services, the user should be able to answer:

```txt
Which agent acted?
On whose behalf?
Using which service?
With which permission?
Through which protocol?
What was the user-visible summary?
Was confirmation required?
What changed?
What evidence or receipt exists?
Can this authority be revoked?
```

The current Internet does not provide a consistent way to answer these questions across services.

AIPS treats audit, traceability, and user confirmation as first-class cross-cutting concerns.

---

## 2. Principles of an Agent-native Internet

### 2.1 Agent-first, human-sovereign

Agent-first does not mean human-absent.

The service interface should be designed for agents, but the authority should remain grounded in the user, organization, or principal that the agent represents. Agents should be powerful executors, not unbounded owners of user authority.

```txt
Optimize the interface for agents.
Keep authority, consent, and accountability anchored to humans or organizations.
```

### 2.2 Protocol before presentation

Human UI should not be the only source of truth.

A service should expose a protocol-level description of what it can do. The visual interface may still exist, but agents should not be forced to reverse-engineer it.

```txt
Presentation is for humans.
Protocol is for agents.
```

### 2.3 Capability as contract

Agents should not guess what a service can do. Services should declare capabilities in a stable, machine-readable form.

A capability profile should answer:

```txt
What is the action?
Which protocol backs it?
Which permissions are required?
What data classes are accessed?
What risk level applies?
Is confirmation required?
Is audit required?
What cost or quota applies?
```

### 2.4 Discovery by declaration, not scraping

Agents should discover services through manifests, Agent Cards, registries, configured endpoints, or local discovery mechanisms. Scraping and visual browsing should be fallback mechanisms, not the primary path.

```txt
If a service wants agents to use it, it should declare how.
```

### 2.5 Delegated authority and least privilege

An agent should receive only the authority necessary for the task.

Authority should be:

- scoped;
- time-limited where possible;
- revocable;
- inspectable;
- bound to a user, organization, or principal;
- separated by risk level.

### 2.6 Explicit confirmation for external consequences

The more external, irreversible, costly, public, or legally meaningful an action is, the more explicit confirmation should be.

Agents may prepare, summarize, compare, and recommend. But actions such as sending, deleting, publishing, purchasing, or legally committing should have confirmation semantics unless a user has clearly delegated that authority in advance.

### 2.7 Auditability by default for high-risk actions

Agent actions should leave enough metadata for later inspection.

Audit metadata should include:

- agent identity;
- service identity;
- user or principal identity;
- capability used;
- backing protocol;
- permission scope;
- risk level;
- confirmation status;
- timestamp;
- result or receipt reference.

### 2.8 Composition, not monopoly

No single protocol should own the entire Agent-first Internet.

A2A is appropriate for agent-to-agent collaboration. MCP is appropriate for tools, resources, prompts, and context. AP2 or similar protocols are appropriate for payments and delegated commerce. OpenAPI and HTTP remain useful for existing APIs.

AIPS should compose them, not replace them.

### 2.9 Human-readable policy, machine-readable enforcement

Policies should be understandable by humans and enforceable by machines.

For example:

```txt
This agent can search files but cannot delete them.
This agent can draft emails but requires confirmation to send.
This agent can compare products but cannot purchase without approval.
```

The same policy should also exist in structured metadata that software can enforce.

### 2.10 Small-service and local-first compatibility

An Agent-first Internet should not require only large platforms to participate.

A personal developer, local app, small website, home server, or internal enterprise service should be able to expose an AIPS-compatible profile with minimal infrastructure.

The minimal path should be simple:

```txt
Expose an existing protocol endpoint.
Add an optional AIPS profile.
Declare capabilities, permissions, risk, confirmation, and audit metadata.
```

---

## 3. What This Changes

### 3.1 From pages to capabilities

The basic unit of the human web is the page.

The basic unit of the Agent-first Internet is the capability.

Instead of asking an agent to navigate through pages, the service declares what it can do and how to call the correct backing protocol.

### 3.2 From browsing to planning

A human browses options visually. An agent plans across capabilities.

In an Agent-first Internet, an agent can compare services by capability, cost, risk, policy, data access, and confirmation requirements before acting.

### 3.3 From accounts to delegated authority

The current web asks: "Is the user logged in?"

The Agent-first Internet asks: "What is this agent allowed to do on behalf of this principal, under which constraints, and with what audit trail?"

### 3.4 From hidden side effects to declared consequences

Human UIs often reveal consequences at the end of a flow. Agent-native services should declare consequences before execution.

For example:

```txt
This action sends an external email.
This action deletes user data.
This action creates a public post.
This action initiates a payment.
This action modifies a calendar event.
```

### 3.5 From SEO to capability discoverability

Human websites compete to be found and interpreted by humans through search engines.

Agent-native services will also need to be found and interpreted by agents. This does not mean replacing search. It means adding structured service discovery, registries, capability indexes, trust metadata, and policy-aware ranking.

### 3.6 From UI testing to protocol validation

Today, many automation workflows break when the UI changes.

In an Agent-first Internet, services can validate:

- profile structure;
- protocol references;
- capability profiles;
- schema links;
- permission requirements;
- confirmation policy;
- audit requirements.

This moves agent integration from brittle UI testing toward protocol validation.

### 3.7 From platform plugins to open interoperability

Many agent platforms expose their own plugin or tool systems. These are useful, but they fragment the ecosystem.

An Agent-first Internet should allow services to declare themselves once and be usable by many agents, frameworks, and platforms.

### 3.8 From attention economy to task economy

The human web monetizes attention. The Agent-first Internet will increasingly monetize task completion, reliability, trust, latency, availability, and delegated execution.

This changes the competitive surface for services. A service that is clear, safe, cheap, auditable, and easy for agents to use may become more valuable than one with a persuasive human interface.
