# Radoslaw Brus

**I break AI agents, then build the controls that stop it — at runtime and at design time.**

Senior AI engineer & architect in Switzerland. 14 years shipping software where failure is expensive: Cloud and Agentic AI systems design and development. Defence edge IoT devices. An agentic AI platform over live building telemetry. Today I build and attack AI agents, measure the tools that do it, and publish the numbers, including the ones where my own tools lose.

[LinkedIn](https://www.linkedin.com/in/radekb/) · [Live red-teaming demo](https://autonomous-ai-red-teaming.web.app/) · **Open to remote roles and contracts from November 2026.**

---

## 🔦 New: sixi-scanner is open source

[**sixi-scanner**](https://github.com/rbrus/sixi-scanner) is a red-team scanner for LLM agents. It's a single Go binary with zero dependencies, 21 techniques and **no LLM inside**: no attacker model, no hosted service, no telemetry. `go install` and scan, or add [`rbrus/scan-action@v2`](https://github.com/rbrus/scan-action) to CI. Output is JSON, SARIF or Markdown, and every finding includes the prompt and reply behind it.

I benchmarked it against six other tools on a real Microsoft Foundry agent behind Azure's strictest content safety, scored from the wire by 10 deterministic oracles and a tool-blind judge:

| sixi-scanner OSS v0.5.0 | | |
|---|---|---|
| **Precision** | **0.452** | 1st (DeepTeam 0.30, promptfoo 0.14, garak 0.14) |
| **Recall** | **0.750** | 1st (garak 0.556) |
| Confirmed violations | 37 from 1,080 turns | 3rd, on half promptfoo's budget |
| Cost | **$0.46** | the only tool with zero cloud inference |

It's the only tool on the board that hits both targets at once. Its gap is breadth: promptfoo landed 89 distinct attacks and garak 67, while sixi landed 7. That gap is shown in the benchmark too.

## 📊 Agent Red-Team Benchmark

[**agent-redteam-benchmark**](https://github.com/rbrus/agent-redteam-benchmark) runs garak, promptfoo, DeepTeam, PyRIT, the Azure AI Red Teaming Agent, sixi-scanner and agent-probe against one Foundry agent. That's about 11,000 turns, with the full protocol and every post-run change disclosed. Some findings:

- **Tools' own reports are mostly noise.** 70–97% of each tool's flags were false alarms, so comparing tools by their self-reported findings compares their noise.
- **It wouldn't *say* its secret, but it *e-mailed* it.** A poisoned KB article got the agent to send a customer's IBAN and a secret code to an attacker, and Prompt Shields didn't flag it.
- **LLM judges miss business logic.** Two 30-EUR refunds get past a 50-EUR-per-request cap. The LLM judge cleared 5 of 7 such cases, while a five-line oracle caught all 7. Score business rules with code.
- **The bill hides in the tool.** 92% of the $79.99 Azure bill was one tool's hosted grading. The target agent itself cost $3.96.

*Conflict of interest, stated in the repo: I maintain the benchmark and two of the tools under test. That's why scoring is oracle-first and tool-blind, and why every raw finding is public.*

## What's here

One loop: **reach an agent → attack it → judge the result → defend what it can touch**, plus checking the design before any of it ships.

| Project | What it does |
|---|---|
| [**sixi-scanner**](https://github.com/rbrus/sixi-scanner) · Go | The scanner above. 21 techniques, zero deps, SARIF. Apache-2.0 |
| [**scan-action**](https://github.com/rbrus/scan-action) · GitHub Action | sixi-scanner in CI: `uses: rbrus/scan-action@v2`, findings in the Security tab, gated on severity. An unreachable agent fails, never passes |
| [**agent-redteam-benchmark**](https://github.com/rbrus/agent-redteam-benchmark) · Python | 7 red-teaming tools vs one real Foundry agent, scored from the wire |
| [**agent-arena**](https://github.com/rbrus/agent-arena) · TypeScript | Evaluation arena with a model-free referee: scripted peers that lie, replay hashes anyone can verify, Diplomacy passing all 164 DATC cases. npm 0.2.3 |
| [**redwire**](https://github.com/rbrus/redwire) · Go | One `Send()` to reach any agent over REST, MCP, A2A, WebSocket or a chat widget. SSRF-guarded |
| [**agent-probe**](https://github.com/rbrus/agent-probe) · Go | One-minute smoke test: 17 probes mapped to the OWASP LLM Top 10, CI exit codes |
| [**c4-guardrails**](https://github.com/rbrus/c4-guardrails) · Go | C4 architecture diagrams linted like code: 11 rules, each citing GDPR / NIS2 / DORA / AI Act. GitHub Action |
| [**agent-redteam-labs**](https://github.com/rbrus/agent-redteam-labs) · Python | Hands-on labs for agent red-teaming and hardening |
| [**adk-demo-target**](https://github.com/rbrus/adk-demo-target) · Python | Deliberately vulnerable bank agent with three defence levels (true negatives matter) |
| [**laya-as-judge**](https://github.com/rbrus/laya-as-judge) · Python | Local LLM-as-a-judge with typed verdicts in milliseconds |
| [**GlassBoxEdge**](https://github.com/rbrus/GlassBoxEdge) · Python | Attackable AI assistant over OT telemetry, with signed data at the edge |

**Upstream:** [`A2ATarget` merged into Microsoft PyRIT](https://github.com/microsoft/PyRIT/pull/2771), so PyRIT can now attack agents over the Agent-to-Agent protocol. [`A2AGenerator` for NVIDIA garak](https://github.com/NVIDIA/garak/pull/2225) is in review.

## Recently

- **Oct** · [scan-action v2](https://github.com/rbrus/scan-action): sixi-scanner as a GitHub Action, SARIF into code scanning.
- **Oct** · sixi-scanner open-sourced. v0.4 → v0.5 took precision 0.27 → 0.45, with recall at 0.75: 1st on both.
- **Sep 30** · `A2ATarget` merged into Microsoft PyRIT.
- **Sep 24–30** · Benchmark baseline plus six disclosed re-runs.
- **Sep 28** · agent-arena 0.2.3 on npm.
- **Sep 26** · agent-probe v1.2.0, redwire CLI, c4-guardrails.

## What I do

- **Adversarial testing of agents:** prompt injection, tool abuse and scope escalation over REST, MCP and A2A; OWASP LLM & Agentic Top 10, MAESTRO.
- **Agent identity & authorization:** on-behalf-of flows, non-human identities, least-privilege MCP tools.
- **AI gateways, guardrails & evals:** Azure API Management, Content Safety, red-teaming in CI, deterministic oracles.
- **Architecture assurance for regulated industries:** DORA, NIS2, GDPR, EU AI Act, with evidence in the pull request.
- **Sovereign edge AI & OT/IoT:** open models on NVIDIA Jetson Thor / DGX Spark, air-gapped inference, agents over live telemetry.

**Stack:** Python · Go · TypeScript · C# / .NET · C/C++ · LangGraph · Google ADK · MCP · A2A · Azure / Microsoft Foundry · GCP · AWS · vLLM · NVIDIA Jetson

**Certified:** Microsoft Multi-Agent AI Solutions Expert (AI-500) · Azure Solutions Architect Expert · Azure Security Engineer · AWS Security Specialty
