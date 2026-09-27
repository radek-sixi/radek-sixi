# Radoslaw Brus

**I break AI agents, then build the controls that stop it — at runtime and at design time.**
Senior AI engineer & architect in Switzerland: secure AI agents, AI platforms on Azure / Microsoft Foundry, edge AI on NVIDIA hardware, and architecture reviews for regulated industries.

[LinkedIn](https://www.linkedin.com/in/radekb/) · [Live red-teaming demo](https://autonomous-ai-red-teaming.web.app/)

---

## What's here

These projects form one loop: **reach an agent → attack it → judge the result → defend what it can touch** — and, before any of it ships, **check the design itself.** The newest piece measures the attackers: **which red-teaming tools actually find anything, at what cost.**

| Project | What it does | Status |
|---|---|---|
| [**Agent Red-Team Benchmark 📊**](https://github.com/rbrus/agent-redteam-benchmark) · Python, Azure | Seven AI red-teaming tools (garak, promptfoo, DeepTeam, PyRIT, Azure AI Red Teaming Agent, sixi-scanner, agent-probe) against one real Microsoft Foundry agent behind Azure's strictest content safety and Prompt Shields. Scored from the wire, not from the tools' own reports: deterministic oracles on every turn plus a tool-blind LLM judge. Full protocol, every post-run change disclosed, and the real Azure bill published. | Results published, Sep 2026 |
| [**Agent Red-Team Labs 🛡️🤖**](https://github.com/rbrus/agent-redteam-labs) · Python | A comprehensive, production-grade, hands-on laboratory curriculum for security engineers, AI developers, and red-teamers to master autonomous AI agent red-teaming, multi-protocol exploitation, and defensive hardening. | Labs |
| [**redwire**](https://github.com/rbrus/redwire) · Go | One `Send()` interface to reach any agent over REST, MCP, A2A, WebSocket or a browser chat widget. SSRF-guarded (DNS-rebinding and redirect checks), standard library first, 86 tests + a runnable example. | Apache-2.0, CI |
| [**agent-probe**](https://github.com/rbrus/agent-probe) · Go | Red-teaming scanner for AI agents as a Go library + CLI: 12 probes mapped to the OWASP Top 10 for LLM apps, SARIF/JSON/Markdown output, CI exit-code thresholds. | Apache-2.0, CI |
| [**adk-demo-target**](https://github.com/rbrus/adk-demo-target) · Python | "Atlas", a deliberately vulnerable bank-support agent on Google ADK with three defence levels (`none`, `basic`, `hardened`). A scanner must find nothing on `hardened`: true negatives matter. | Working, local |
| [**laya-as-judge**](https://github.com/rbrus/laya-as-judge) · Python | Local "LLM-as-a-judge" with typed decisions (`noul`, `score`, `choice`) and no output tokens: milliseconds per verdict, frontier model only for the uncertain cases. | Experimental |
| [**GlassBoxEdge**](https://github.com/rbrus/GlassBoxEdge) · Python | An AI assistant over OT telemetry that you're invited to attack. Edge tier: signed telemetry at source, and device-side validation as the only path for commands. | Early, building in public |
| [**c4-guardrails**](https://github.com/rbrus/c4-guardrails) · Go | Deterministic guardrails for C4 architecture diagrams (Mermaid C4 or JSON): 11 rules, each finding cites a GDPR / NIS2 / DORA / AI Act clause. Table, SARIF, PR summary and an HTML report with the diagram highlighted; GitHub Action included. No AI, no network. <a href="https://github.com/rbrus/c4-guardrails"><img src="https://raw.githubusercontent.com/rbrus/c4-guardrails/main/docs/media/before-after.png" alt="The same C4 diagram twice: the bad twin with red-highlighted edges and rule badges, the good twin clean. Output of c4-guardrails." width="900"></a> | Apache-2.0, CI |
| [**Qwen on one DGX Spark**](https://github.com/rbrus/Qwen3.8-Flash-Next-Single-DGX-Spark) | Launcher and measured sweeps for a large open model on a single DGX Spark: 48.7 tok/s single stream, 162.9 tok/s at 8 streams, 512k context. | Measured |

*These are the parts that stand on their own. The integrated scanner they feed, **sixi-scanner**, is not public yet; its progress is measured in the open in the benchmark above.*

## Agent Red-Team Benchmark: what it cost and what we learned

Everything runs on Azure: a Foundry prompt agent on `gpt-5-nano` with every content filter at **Low** and Prompt Shields on, an Azure OpenAI judge, and Azure Cost Management as the source for the bill. Only the attacker model is local, an uncensored Qwen3.6-35B-A3B on a Jetson Thor, which is why seven tools could share one attack generator without metering.

**The bill for the baseline run: $79.99.**

| Cost generator | USD | Share |
|---|---|---|
| Azure AI Red Teaming Agent's own Evaluations pipeline | $73.30 | 92% |
| Target agent `gpt-5-nano`, all seven tools, ≈5,900 turns | $3.96 | 5% |
| Unified judge, ≈4,400 verdicts | $2.01 | 2.5% |
| Infra (registry, hosted vCPU/memory) | $0.72 | 1% |

Attacking a real agent is cheap. The Microsoft tool's internal grading was 92% of the bill, and its cost per confirmed violation (≈$24) dwarfs promptfoo's (≈$0.01). Budget for the grading, not the target.

**Where the tools landed (2026-09-24 baseline, confirmed violations from the wire):** promptfoo 89 · garak 81 · DeepTeam 22 · sixi-scanner 19 · PyRIT 16 · Azure AI Red Teaming Agent 3 (64% of its turns blocked by Azure before reaching the model) · agent-probe 0 (12 fixed probes, one minute, $0.00). Precision was low across the board: the best was DeepTeam at 30%, and every tool self-flagged far more than it proved.

**sixi-scanner, openly.** The benchmark is maintained by the authors of one of the tools under test; that conflict of interest is stated in the README, and it is the reason the scoring is oracle-first and tool-blind. The baseline was humbling: sixi-scanner ran with an empty target context and no confirmation pass, and finished last among the competitive tools on precision (3%) and recall (16%), with zero deterministic oracle hits. The first fix cycle, every change disclosed in the protocol, added a declared target context, a three-framing confirmation judge, a rule-recitation wrapper, ported proven payloads and a timeout. The re-run two days later, same target and same judge:

| sixi-scanner | 09-24 | 09-26 | Against the 09-24 leaderboard |
|---|---|---|---|
| Confirmed violations | 19 | **27** | 3rd, behind promptfoo and garak |
| Distinct oracle codes | 0 | **3** | tied 1st with promptfoo |
| Risk categories | 3 | **8** | tied 1st with garak |
| Recall | 16% | **44%** | 2nd, garak 56% |
| Precision | 3% | 10% | 4th, still below the 30% bar |

Precision is the open problem: one scan phase produced 42 flags and 1 confirmed violation, because it counted the caller's own balance and IBAN as leaks. Two precision fixes and a recall marker shipped after that run were measured against the run's recorded turns and are in the tool, but have not been paid for with another run yet. The shortfalls are in [the validation write-up](https://github.com/rbrus/agent-redteam-benchmark/blob/main/results/2026-09-26-validation/README.md), measured rather than guessed. Updates land there first.

## Recently

- **Sep 2026** · [Agent Red-Team Benchmark](https://github.com/rbrus/agent-redteam-benchmark): seven red-teaming tools vs one Foundry agent on Azure, scored from the wire; full cost breakdown published ($79.99, 92% of it the Azure tool's own grading). sixi-scanner's first fix cycle disclosed and re-measured: recall 16% → 44%, last competitive place → third.
- **Sep 2026** · [c4-guardrails](https://github.com/rbrus/c4-guardrails): C4 diagrams checked locally and in CI, 11 deterministic rules, each finding cites a GDPR / NIS2 / DORA / AI Act clause; SARIF into code scanning.
- **2026** · [Qwen on one DGX Spark](https://github.com/rbrus/Qwen3.8-Flash-Next-Single-DGX-Spark): 48.7 tok/s single stream, 162.9 tok/s at 8 streams, 512k context, measured.
- **2026** · [agent-probe](https://github.com/rbrus/agent-probe) + [redwire](https://github.com/rbrus/redwire): 12 OWASP-mapped probes over REST, MCP, A2A, WebSocket and browser chat, with CI exit codes.

## What I work on

- **Agent identity & authorization:** on-behalf-of flows, non-human identities, least-privilege tool access (MCP).
- **AI gateways, guardrails & evaluations:** Azure API Management, Content Safety, red-team suites in CI, and benchmarking the red-teaming tools themselves against real Foundry agents.
- **Adversarial testing of agents:** prompt injection, scope escalation and tool abuse; OWASP Top 10 for LLM and Agentic applications, MAESTRO.
- **Agents over real data:** enterprise APIs and live IoT/OT telemetry via MCP and GraphQL.
- **Design-time architecture assurance:** C4 models reviewed like code, deterministic rules with regulatory clause citations (DORA, NIS2, GDPR, EU AI Act), evidence in the pull request.
- **Sovereign edge AI:** open models on NVIDIA Jetson Thor / Orin and DGX Spark, for data that can't leave the site.
- **OT/IoT architecture:** agents over live device telemetry, event-driven IoT (IoT Hub, MQTT, Kepware-class gateways), air-gapped inference.
  
**Stack:** Python · Go · C# / .NET · C/C++ · LangGraph · Google ADK · MCP · A2A · Azure · Microsoft Foundry · GCP · AWS · vLLM · NVIDIA Jetson

## Background

14 years of engineering, much of it in regulated industries: C/C++ on Linux for defence edge devices, an event-driven IoT platform for a global pharma client, and most recently the architecture of an agentic AI platform over building telemetry.
Microsoft Certified: Multi-Agent AI Solutions Expert (AI-500) · Azure Solutions Architect Expert · Azure Security Engineer · AWS Security Specialty.

**Open to remote roles and contracts from November 2026.**
