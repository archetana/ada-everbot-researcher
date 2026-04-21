# Cost of Running an Agent: Enterprise Analysis for AWS Bedrock AgentCore

> **Revised:** April 21, 2026 — Corrected session counts, model updated to Claude Sonnet 4.5, batch inference added, Azure comparison included, cache pricing fixed.
>
> **AWS Calculator:** https://calculator.aws/#/estimate?id=76f47c5a48ccb59c85d891eede6041259a054f2d
> **Azure Calculator:** https://azure.com/e/a09c9e21bbbb48838134b741362ed0da

---

## Overview

This document provides a comprehensive analysis of the total cost of running AI agents using AWS Bedrock AgentCore for enterprise deployment. It includes all cost components required for production, with corrections applied from the initial estimate and an Azure OpenAI comparison for model inference costs.

**Reference use case:** Tier 1 US-Based Retail Bank — Fraud Prevention and Credit Services.

---

## Use Case and Justification for Selected Values

- **25 Agents across 10 business use cases:** 5 QC processes × 5 agents per process = 25 agents (e.g., fraud screening, credit assessment, onboarding, report generation, compliance monitoring).
- **1,000 invocations per agent per month:** Each agent processes 1,000 work items monthly.
- **Total monthly sessions: 25,000** (25 agents × 1,000 invocations).
- **Model — Claude Sonnet 4.5:** Current-generation mid-tier model. Sufficient reasoning capability for financial QC (complex regulatory logic, tool orchestration) without the cost premium of frontier models.
- **6,000 input / 800 output tokens per session:** Input accommodates large system prompts (Basel III, CCAR, GDPR guardrails) plus tool schemas for core banking APIs. Output reflects concise structured responses.
- **95% I/O wait time:** Reflects legacy banking middleware latency. On AgentCore, CPU is only billed during active processing — I/O wait is free. This substantially reduces compute cost vs. traditional VM-based deployments.
- **Geo Cross-Region Inference:** Routes requests to the nearest available region for resilience. No cost premium at this throughput.

---

## Corrected Monthly Cost Breakdown (AWS)

Derived from AWS Pricing Calculator export dated April 21, 2026.

| Service | Configuration | Monthly | Annual |
|---------|--------------|---------|--------|
| AgentCore Runtime | 25,000 sessions · 300s avg · 95% I/O wait · 2 vCPU · 1 GB | $38.33 | $459.99 |
| AgentCore Gateway | 10,000 sessions* · 10 tools · 5 invocations/session | $0.75 | $9.02 |
| AgentCore Memory | 10,000 sessions* · 5 turns/session · 100s duration | $19.29 | $231.50 |
| AgentCore Observability | 1.5 GB logs/month · 5 GB spans/month (CloudWatch) | $2.50 | $30.00 |
| Claude Sonnet 4.5 — Standard | 3 req/min · 24h/day · 6,000 input + 800 output tokens | $2,198.30 | $26,379.55 |
| Claude Sonnet 4.5 — Batch | 50,000 records/month · 6,000 input + 800 output tokens | $825.00 | $9,900.00 |
| **TOTAL** | | **$3,084.17** | **$37,010.04** |

*AgentCore Gateway and Memory are currently configured at 10,000 sessions in the calculator. These should be updated to 25,000 to match Runtime on the next revision.*

### Key Corrections from the Original Estimate

The original PDF estimate (April 21, 2026) showed a monthly total of $1,573,161 due to a misconfigured Bedrock line item: 2,315 requests/minute had been entered instead of the correct 2.9 req/min (125,000 requests/month ÷ 43,200 minutes). This single error inflated the Bedrock inference cost from ~$2,200 to ~$1,542,000. The corrected figure is $3,084.17/month.

---

## Batch Inference

Batch jobs run asynchronously and are 50% cheaper than on-demand, making them ideal for non-time-sensitive workflows.

**Inputs used:**
- Total records: 50,000/month (approximately 2× real-time sessions — covers nightly fraud sweeps, credit review queues, end-of-day report generation across 25 agents)
- Input tokens per record: 6,000
- Output tokens per record: 800

**Monthly cost: $825.00**

This is a conservative proxy. Refine after a proof-of-concept with actual batch job volumes.

**Appropriate batch workloads for banking:**
- Nightly fraud analysis runs — reviewing all accounts with activity that day
- Scheduled report generation — end-of-day summaries across all 25 agents
- Bulk document processing — onboarding documents, credit applications, policy PDFs ingested into knowledge bases

---

## AgentCore Services Reference

### Runtime
Serverless compute purpose-built for AI agents. Billing is per-second based on actual CPU consumption and peak memory. I/O wait during LLM calls, API calls, or database queries incurs no CPU charge — only memory billing continues. Includes a 1-second minimum and 128 MB minimum memory billing.

### Gateway
Enables agents to securely discover and invoke tools. Transforms APIs, Lambda functions, and existing services into agent-compatible tools. Connects to MCP servers. Priced per MCP operation (ListTools, CallTool, Ping), search query, and tool indexed.

### Memory
Manages short-term (in-session) and long-term (cross-session) memory. Short-term priced per create-event request; long-term priced per stored memory record per day and per retrieval call.

### Identity
Manages OAuth tokens and API keys for agent authentication. **Available at no additional charge** when used through AgentCore Runtime or AgentCore Gateway.

### Policy
Checks every tool call against authorization rules. Natural language policy authoring converts descriptions to Cedar policies (charged per 1,000 input tokens). **Not yet integrated into the standard AWS Pricing Calculator — price manually.**

### Observability
Full telemetry ingested into Amazon CloudWatch — traces, logs, and spans. Priced via CloudWatch ingestion and storage rates.

### Evaluations
Built-in evaluators for agent quality monitoring. Priced per input/output tokens processed. Custom evaluations incur additional model usage charges. **Not yet in AWS Pricing Calculator.**

### AWS Agent Registry (Preview)
Centralized catalog for MCP servers, agents, and skills. Includes a free tier: 5,000 records, 1M Search API calls, and 2M Get/List calls per month. Charges apply above these thresholds.

---

## Prompt Caching (Not Yet Applied to Calculator)

Prompt caching reduces inference cost for workloads with repeated system prompts (e.g., static regulatory headers). It is not currently modeled in the calculator but should be added as a separate line item.

**Correct Claude Sonnet 4.5 cache pricing:**
- Cache writes: **$0.00375 per 1,000 tokens** (25% premium over standard input — not $0.003 as previously stated)
- Cache reads: **$0.0003 per 1,000 tokens** (90% cheaper than standard input)

At a 90% cache hit rate with 6,000 input tokens per session across 25,000 sessions, adding caching could reduce total inference cost by 40–60%. A realistic production cache hit rate for banking agents is 50–70%; the 90% figure is optimistic.

---

## Azure OpenAI Comparison (GPT-4o-1120, East US)

For equivalent token volumes (Standard: 750K input + 100K output ×1K tokens; Batch: 300K input + 40K output ×1K tokens).

| Option | Monthly | Notes |
|--------|---------|-------|
| Standard (On-Demand) | $2,874.97 | Pay per token · Global · Real-time |
| Batch (On-Demand) | $575.00 | 50% off standard · Async |
| **Standard + Batch combined** | **$3,449.97** | Comparable to AWS total |
| Provisioned (PTU) — 50 units | $36,500.00 | Fixed cost regardless of usage |

**PTU assessment:** At current session volume, 50 PTUs (minimum for Global deployment) deliver approximately 5% utilization. The break-even point against on-demand pricing is approximately 250,000 sessions/month — roughly 10× current volume. PTU is not recommended at this stage.

**Model note:** GPT 4.1 is a closer capability peer to Claude Sonnet 4.5 than GPT-4o-1120. Update the Azure model selection on the next calculator revision.

**PTU note:** PTU is not available for Claude Sonnet 3.7 on Azure.

---

## Services Not Yet Included in Estimate

### Priority 1 — Add Next (High Cost or Compliance-Critical)

| Service | Est. Monthly | Why Needed |
|---------|-------------|------------|
| Amazon Bedrock Guardrails | $500–$2,000 | PII detection, content filtering, topic guardrails. Non-negotiable for banking. Not yet in AWS Pricing Calculator. |
| Amazon OpenSearch Serverless | $700–$2,000 | Vector store for RAG / Knowledge Bases. Required if agents query fraud rules, credit policies, or documents. |
| VPC Endpoints (PrivateLink) | $50–$200 | Keeps Bedrock and AgentCore traffic off the public internet. Mandatory for Tier 1 bank security policy. |
| AWS CloudTrail (Data Events) | $50–$300 | Full API audit trail. Required for banking regulatory compliance (SOC2, PCI-DSS). |
| AWS KMS | $10–$50 | Encryption key management for data at rest. Banking compliance requirement. |

### Priority 2 — Add to Next Revision (Operationally Necessary)

| Service | Est. Monthly | Why Needed |
|---------|-------------|------------|
| Amazon API Gateway | $50–$500 | Exposes agents as APIs to internal banking apps and portals. |
| AWS Lambda | $20–$200 | Executes tool functions invoked by agents (~125K+ calls/month at this session volume). |
| Amazon DynamoDB | $20–$100 | Session state, conversation history, and persistent agent context. |
| Amazon S3 | $10–$50 | Knowledge base documents, batch artifacts, direct-code deployment storage. |

**Estimated total with Priority 1 services added: $5,000–$15,000 additional per month.** Total inclusive estimate: **$8,000–$18,000/month**, subject to actual Guardrails and OpenSearch usage.

---

## Cost Per Transaction

Based on the current AWS estimate of $3,084.17/month across 25,000 sessions:

- **Cost per session:** $0.123
- **Cost per agent per month:** $3,084.17 ÷ 25 = **$123.37**
- **Cost per 1,000 sessions:** **$123.37**

These figures will increase once Priority 1 services are added.

---

## Cost Optimization Strategies

1. **Batch routing:** Send all non-time-sensitive workloads through batch inference (50% savings). Target: fraud sweeps, credit reviews, report generation.
2. **Prompt caching:** Implement caching for static system prompt sections (regulatory context, tool schemas). Projected 40–60% inference cost reduction.
3. **Right-size Gateway and Memory:** Update session counts to 25,000 to align with Runtime; recalculate on next revision.
4. **Cache hit rate sensitivity:** Model at 50%, 70%, and 90% hit rates to bound the range before committing to caching infrastructure.
5. **Knowledge base query optimization:** Minimize RAG retrievals per session; retrieve once and pass context to reduce OpenSearch costs.
6. **Grow into PTU:** Revisit Azure PTU if sessions scale to 250K+/month.

---

## What Still Needs Pricing

The following AgentCore components are not yet available in the standard AWS Pricing Calculator and must be estimated manually from the pricing page at [aws.amazon.com/bedrock/agentcore/pricing](https://aws.amazon.com/bedrock/agentcore/pricing/):

- **AgentCore Policy** — authorization checks per tool call
- **AgentCore Evaluations** — built-in evaluators (tokens billed by AgentCore)
- **Amazon Bedrock Guardrails** — per text unit processed
- **Amazon Bedrock Knowledge Bases** — per retrieval request

---

## Recommendations

1. **Use AWS on-demand at current volume.** At $3,084/month, it is competitive with Azure on-demand ($3,450) and far more cost-effective than Azure PTU ($37,075).
2. **Route batch workloads explicitly.** Separate real-time (3 req/min, 24h) from non-urgent (50K records/month batch) to capture the 50% batch discount.
3. **Add Guardrails and OpenSearch before sharing with procurement.** These are likely the second and third largest line items once added.
4. **Run a PoC to validate assumptions.** Specifically: actual I/O wait percentage, realistic cache hit rate, and batch job volumes.
5. **Set up cost alerting on Day 1.** AWS Cost Explorer budgets with alerts at 80% and 100% of monthly estimate. Tag all AgentCore resources for per-agent cost allocation.

---

## References

1. [Amazon Bedrock AgentCore Pricing](https://aws.amazon.com/bedrock/agentcore/pricing/) — AWS
2. [Amazon Bedrock AgentCore Overview](https://aws.amazon.com/bedrock/agentcore/) — AWS
3. [AgentCore Developer Guide](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/what-is-bedrock-agentcore.html) — AWS
4. [AWS Bedrock Pricing: Hidden Costs, Real Savings](https://aws.cloudshim.com/aws-top-services/amazon-bedrock) — CloudShim
5. [Predicting Your AI Agent's Cost](https://dev.to/aws/predicting-your-ai-agents-cost-6m9) — AWS Dev Community

---

*Analysis revised: April 21, 2026*
*Estimates based on public AWS and Azure pricing calculators. Actual costs depend on real usage. Verify with AWS and Azure representatives for enterprise contract pricing.*
