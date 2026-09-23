# Radoslaw Brus

**I break AI agents, then build the controls that stop it.**
Senior AI engineer & architect in Switzerland: secure AI agents, AI platforms on Azure / Microsoft Foundry, and edge AI on NVIDIA hardware.

[LinkedIn](https://www.linkedin.com/in/radekb/) · [Live red-teaming demo](https://autonomous-ai-red-teaming.web.app/)

---

## What's here

Four projects form one loop: **reach an agent → attack it → judge the result → defend what it can touch.**

| Project | What it does | Status |
|---|---|---|
| [**redwire**](https://github.com/rbrus/redwire) · Go | One `Send()` interface to reach any agent over REST, MCP, A2A, WebSocket or a browser chat widget. SSRF-guarded (DNS-rebinding and redirect checks), standard library first, 85 tests. | Working, CI |
| [**agent-probe**](https://github.com/rbrus/agent-probe) · CLI | Red-teaming scanner for AI agents: 12 probes mapped to the OWASP Top 10 for LLM apps, SARIF/JSON/Markdown output, CI exit-code thresholds. | R&D edition |
| [**adk-demo-target**](https://github.com/rbrus/adk-demo-target) · Python | "Atlas", a deliberately vulnerable bank-support agent on Google ADK with three defence levels (`none`, `basic`, `hardened`). A scanner must find nothing on `hardened`: true negatives matter. | Working, local |
| [**laya-as-judge**](https://github.com/rbrus/laya-as-judge) · Python | Local "LLM-as-a-judge" with typed decisions (`noul`, `score`, `choice`) and no output tokens: milliseconds per verdict, frontier model only for the uncertain cases. | Experimental |
| [**GlassBoxEdge**](https://github.com/rbrus/GlassBoxEdge) · Python | An AI assistant over OT telemetry that you're invited to attack. Edge tier: signed telemetry at source, and device-side validation as the only path for commands. | Early, building in public |
| [**Qwen on one DGX Spark**](https://github.com/rbrus/Qwen3.8-Flash-Next-Single-DGX-Spark) | Launcher and measured sweeps for a large open model on a single DGX Spark: 48.7 tok/s single stream, 162.9 tok/s at 8 streams, 512k context. | Measured |

## What I work on

- **Agent identity & authorization:** on-behalf-of flows, non-human identities, least-privilege tool access (MCP).
- **AI gateways, guardrails & evaluations:** Azure API Management, Content Safety, red-team suites in CI.
- **Adversarial testing of agents:** prompt injection, scope escalation and tool abuse; OWASP Top 10 for LLM and Agentic applications, MAESTRO.
- **Agents over real data:** enterprise APIs and live IoT/OT telemetry via MCP and GraphQL.
- **Sovereign edge AI:** open models on NVIDIA Jetson Thor / Orin and DGX Spark, for data that can't leave the site.
- **OT/IoT architecture:** agents over live device telemetry, event-driven IoT (IoT Hub, MQTT, Kepware-class gateways), air-gapped inference.
  
**Stack:** Python · Go · C# / .NET · C/C++ · LangGraph · Google ADK · MCP · A2A · Azure · Microsoft Foundry · GCP · AWS · vLLM · NVIDIA Jetson

## Background

14 years of engineering, much of it in regulated industries: C/C++ on Linux for defence edge devices, an event-driven IoT platform for a global pharma client, and most recently the architecture of an agentic AI platform over building telemetry.
Microsoft Certified: Multi-Agent AI Solutions Expert (AI-500) · Azure Solutions Architect Expert · Azure Security Engineer · AWS Security Specialty.

**Open to remote roles and contracts from November 2026.**
