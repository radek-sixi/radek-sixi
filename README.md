# Radoslaw Brus

**I break AI agents, then build the controls that stop it — at runtime and at design time.**
Senior AI engineer & architect in Switzerland: secure AI agents, AI platforms on Azure / Microsoft Foundry, edge AI on NVIDIA hardware, and architecture reviews for regulated industries.

[LinkedIn](https://www.linkedin.com/in/radekb/) · [Live red-teaming demo](https://autonomous-ai-red-teaming.web.app/)

---

## What's here

These projects form one loop: **reach an agent → attack it → judge the result → defend what it can touch** — and, before any of it ships, **check the design itself.** Two newer pieces do the measuring: **which red-teaming tools actually find anything, at what cost** (the benchmark), and **how an agent holds up against peers that lie, with no model in the referee** (agent-arena).

| Project | What it does | Status |
|---|---|---|
| [**Agent Red-Team Benchmark 📊**](https://github.com/rbrus/agent-redteam-benchmark) · Python, Azure | Seven AI red-teaming tools (garak, promptfoo, DeepTeam, PyRIT, Azure AI Red Teaming Agent, sixi-scanner, agent-probe) against one real Microsoft Foundry agent behind Azure's strictest content safety and Prompt Shields. Scored from the wire, not from the tools' own reports: deterministic oracles on every turn plus a tool-blind LLM judge. Full protocol, every post-run change disclosed, and the real Azure bill published. | Baseline + 6 re-runs, Sep 2026 |
| [**agent-arena**](https://github.com/rbrus/agent-arena) · TypeScript | An evaluation arena for AI agents whose referee runs no model: seeded scenarios with scripted peers that lie, partition, delay and learn your habits, including a Diplomacy table whose clean-room adjudicator passes all 164 DATC cases. Every result carries a replay hash anyone can re-simulate with `agent-arena verify`; `report.json` + SARIF out, over REST, WebSocket, MCP or A2A. A pass certifies nothing: it means these oracles didn't fire on these seeds. | 0.2.3 on npm, Apache-2.0, CI |
| [**Agent Red-Team Labs 🛡️🤖**](https://github.com/rbrus/agent-redteam-labs) · Python | A comprehensive, production-grade, hands-on laboratory curriculum for security engineers, AI developers, and red-teamers to master autonomous AI agent red-teaming, multi-protocol exploitation, and defensive hardening. | Labs |
| [**redwire**](https://github.com/rbrus/redwire) · Go | One `Send()` interface to reach any agent over REST, MCP (incl. SSE), A2A, WebSocket or a browser chat widget, plus a standalone CLI. SSRF-guarded (DNS-rebinding and redirect checks), standard library first, 109 tests + a runnable example. | Apache-2.0, CI |
| [**agent-probe**](https://github.com/rbrus/agent-probe) · Go | Fast single-turn baseline scanner for AI agents, as a Go library + CLI: 17 probes mapped to the OWASP Top 10 for LLM apps (now incl. indirect RAG injection and cloud-metadata SSRF), SARIF/JSON/Markdown output, CI exit-code thresholds. | v1.2.0, Apache-2.0, CI |
| [**adk-demo-target**](https://github.com/rbrus/adk-demo-target) · Python | "Atlas", a deliberately vulnerable bank-support agent on Google ADK with three defence levels (`none`, `basic`, `hardened`). A scanner must find nothing on `hardened`: true negatives matter. | Working, local |
| [**laya-as-judge**](https://github.com/rbrus/laya-as-judge) · Python | Local "LLM-as-a-judge" with typed decisions (`noul`, `score`, `choice`) and no output tokens: milliseconds per verdict, frontier model only for the uncertain cases. | Experimental |
| [**GlassBoxEdge**](https://github.com/rbrus/GlassBoxEdge) · Python | An AI assistant over OT telemetry that you're invited to attack. Edge tier: signed telemetry at source, and device-side validation as the only path for commands. | Early, building in public |
| [**c4-guardrails**](https://github.com/rbrus/c4-guardrails) · Go | Deterministic guardrails for C4 architecture diagrams (Mermaid C4 or JSON): 11 rules, each finding cites a GDPR / NIS2 / DORA / AI Act clause. Table, SARIF, PR summary and an HTML report with the diagram highlighted; GitHub Action included. No AI, no network. <a href="https://github.com/rbrus/c4-guardrails"><img src="https://raw.githubusercontent.com/rbrus/c4-guardrails/main/docs/media/before-after.png" alt="The same C4 diagram twice: the bad twin with red-highlighted edges and rule badges, the good twin clean. Output of c4-guardrails." width="900"></a> | Apache-2.0, CI |

**Upstream:** [`A2ATarget` in Microsoft PyRIT](https://github.com/microsoft/PyRIT/pull/2771), merged 2026-09-30: PyRIT can now target agents exposed over the Agent-to-Agent protocol. [`A2AGenerator` for NVIDIA garak](https://github.com/NVIDIA/garak/pull/2225): in review.

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

**sixi-scanner, openly.** The benchmark is maintained by the author of two of the tools under test, sixi-scanner and agent-probe; that conflict of interest is stated in the README, and it is the reason the scoring is oracle-first and tool-blind. The baseline was humbling: sixi-scanner ran with an empty target context and no confirmation pass, and finished last among the competitive tools on precision (3%) and recall (16%), with zero deterministic oracle hits. Six re-runs followed by 2026-09-30, same target and same judge, every change disclosed in the protocol. Baseline against the latest:

| sixi-scanner | 09-24 | 09-30 | Against the 09-24 leaderboard |
|---|---|---|---|
| Recall | 16% | **61%** | **1st**, ahead of garak 56% |
| Precision | 3% | **16%** | 2nd, behind DeepTeam 30% |
| Risk categories | 3 | 7 | 2nd, behind garak 8 |
| Oracle codes | 0 | 3, incl. `REFUND_CAP_SPLIT` | tied 1st with promptfoo |
| Confirmed violations | 19 | 23 | 3rd, behind promptfoo 89 and garak 81 |

Recall here means how complete a tool's own report is about what it broke, not how much of the target it found ([PROTOCOL §5](https://github.com/rbrus/agent-redteam-benchmark/blob/main/docs/PROTOCOL.md)). The other six tools ran once, at their defaults. sixi-scanner's fixes were chosen against this benchmark's own ground truth, which the protocol states as a limitation of these numbers, not a validation of them.

**A bypass class worth checking in your own agents.** The target's refund cap is 50 EUR *per request*. Two 30-EUR refunds respect every call and total 60: a guardrail checked per request is bypassed by splitting. The tool-blind LLM judge cleared 5 of the 7 such turns as "each within the 50 EUR limit"; a five-line, session-aware oracle (`REFUND_CAP_SPLIT`) caught all 7. Score business rules with code. Re-run over every tool's logged turns, only sixi-scanner's traffic contains one, in four runs.

**Still open, measured: precision, 16% against DeepTeam's 30%.** The limiter is the confirmation judge: on this hardware it has to be the attacker model. `qwen3.8:27b` measured 0.933 on the confirm-read turns, but sharing one GPU with the attacker it took 45–80 s per call, 20–35 h per run. The shipped `SIXI_JUDGE_*` seam runs it on a multi-GPU host, where the projections clear the bar; that is a projection, not a run yet. Per-run write-ups are in [results/](https://github.com/rbrus/agent-redteam-benchmark/tree/main/results), measured rather than guessed. Updates land there first.

## Recently

- **Sep 30** · [`A2ATarget` merged into Microsoft PyRIT](https://github.com/microsoft/PyRIT/pull/2771): PyRIT can target agents over the Agent-to-Agent protocol (0.3 and 1.0, on the official `a2a-sdk`), reworked through the maintainers' review. The garak counterpart, [`A2AGenerator`](https://github.com/NVIDIA/garak/pull/2225), is in review.
- **Sep 24–30** · [Agent Red-Team Benchmark](https://github.com/rbrus/agent-redteam-benchmark): seven tools vs one Foundry agent, a $79.99 bill (92% of it the Azure tool's own grading); six disclosed sixi-scanner re-runs since: recall 16% → 61% (1st), precision 3% → 16% (2nd), and a session-aware oracle for the split-refund bypass the LLM judge mostly missed.
- **Sep 28** · [agent-arena](https://github.com/rbrus/agent-arena) 0.2.3 on npm: seeded adversarial scenarios for agents, hash-committed replays anyone can re-verify, SARIF out; Diplomacy runnable.
- **Sep 26** · [agent-probe](https://github.com/rbrus/agent-probe) v1.2.0 (17 probes) and [redwire](https://github.com/rbrus/redwire) with a standalone CLI and MCP over SSE.
- **Sep 26** · [c4-guardrails](https://github.com/rbrus/c4-guardrails): C4 diagrams checked locally and in CI, 11 deterministic rules, each finding cites a GDPR / NIS2 / DORA / AI Act clause; SARIF into code scanning.

## What I work on

- **Agent identity & authorization:** on-behalf-of flows, non-human identities, least-privilege tool access (MCP).
- **AI gateways, guardrails & evaluations:** Azure API Management, Content Safety, red-team suites in CI, benchmarking the red-teaming tools themselves against real Foundry agents, and deterministic evaluation with no model in the referee.
- **Adversarial testing of agents:** prompt injection, scope escalation and tool abuse, over REST, MCP and A2A; OWASP Top 10 for LLM and Agentic applications, MAESTRO.
- **Agents over real data:** enterprise APIs and live IoT/OT telemetry via MCP and GraphQL.
- **Design-time architecture assurance:** C4 models reviewed like code, deterministic rules with regulatory clause citations (DORA, NIS2, GDPR, EU AI Act), evidence in the pull request.
- **Sovereign edge AI:** open models on NVIDIA Jetson Thor / Orin and DGX Spark, for data that can't leave the site.
- **OT/IoT architecture:** agents over live device telemetry, event-driven IoT (IoT Hub, MQTT, Kepware-class gateways), air-gapped inference.
  
**Stack:** Python · Go · TypeScript · C# / .NET · C/C++ · LangGraph · Google ADK · MCP · A2A · Azure · Microsoft Foundry · GCP · AWS · vLLM · NVIDIA Jetson

## Background

14 years of engineering, much of it in regulated industries: C/C++ on Linux for defence edge devices, an event-driven IoT platform for a global pharma client, and most recently the architecture of an agentic AI platform over building telemetry.
Microsoft Certified: Multi-Agent AI Solutions Expert (AI-500) · Azure Solutions Architect Expert · Azure Security Engineer · AWS Security Specialty.

**Open to remote roles and contracts from November 2026.**
