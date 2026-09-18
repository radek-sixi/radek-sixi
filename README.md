## Radoslaw Brus

Architect by heart, engineer by hands. I build security tooling for AI systems, from the cloud they
run on to the agents running on it. Based in Switzerland.

I started in cloud security, building vulnerability scanners for Azure and AWS. As agents and
LLM-powered systems began shipping into production, the interesting attack surface moved with them,
and so did I. Most of my recent work is red-teaming autonomous agents and the infrastructure around
them.

### Sixi AI — founder, sole engineer

An agentic red-teaming platform for LLM agents, built and run from Switzerland.
Live at [sixi.ch](https://sixi.ch).

- 46 attack agents and 318 techniques, mapped to OWASP LLM Top 10, MITRE ATLAS, OWASP Agentic AI
  Threats, the EU AI Act and GDPR
- One Go binary, standard library first. Ported from Python in full and cut over in production
- Five transports: REST, MCP, A2A, WebSocket, and a browser chat widget driven over the Chrome
  DevTools Protocol
- A broken-target acceptance suite of 166 cells that points the real binary at dead, lying and
  half-broken endpoints. It found defects the unit suite, green throughout, never saw
- Ships two ways: a hosted trial on Cloud Run in Zurich, and a sealed package with an offline
  Ed25519 licence for a customer's own network

### Selected open source

- **[redwire](https://github.com/rbrus/redwire)** — reach an AI agent over REST, MCP, A2A, WebSocket,
  or a browser chat widget through one Go interface. SSRF-guarded by default, standard library
  first, 86 behavioural tests. The transport layer of Sixi AI. Apache-2.0.
- **[scan-action](https://github.com/sixi-ai/scan-action)** — a GitHub Action that red-teams an
  agent endpoint and files the findings as SARIF in the Security tab.
- **[Atlas](https://github.com/rbrus/adk-demo-target)** — a customer-support agent for a bank that
  does not exist. Built with Google's Agent Development Kit, and its job is to be broken.
- **[GlassBox Edge](https://github.com/rbrus/GlassBoxEdge)** — an AI assistant grounded in live OT
  telemetry, running on edge nodes, built to be attacked.

### Focus

- **AI / LLM security** — red-teaming agents, prompt injection and tool abuse, evidence that stands
  up to an auditor
- **Zero-Trust and cloud architecture** — Azure, GCP; identity, network and data-residency
  boundaries
- **Edge and IoT** — running models and security workloads on constrained hardware (Jetson)

### Stack

Go · Python · TypeScript · .NET · Azure / GCP / AWS · LangGraph · Google ADK · Jetson ·
Firestore / SQLite

### Contact

Open to AI security and platform engineering roles.
[LinkedIn](https://www.linkedin.com/in/radekb/)
