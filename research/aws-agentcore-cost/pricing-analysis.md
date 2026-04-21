# Cost of Running an Agent: Enterprise Analysis for AWS Bedrock AgentCore

## Overview
This document provides a comprehensive analysis of the total cost of running AI agents using AWS Bedrock AgentCore for enterprise deployment. It includes all cost components required for running agents in production, explained in executive-friendly terms for stakeholder communication.

## Specific Use Case: Tier 1 US-Based Retail Bank - Fraud Prevention/Credit Services
Based on AWS Calculator estimation (https://calculator.aws/#/estimate?id=19c2da06e479d0010c03861ae06628c18f94d044), we have modeled a Tier 1 US-based Retail Bank in a full production environment, specifically high-stakes departments like Fraud Prevention or Credit Services.

**Justification for Selected Values:**
- **25 Agents for 10 Use Cases**: The bank requires 25 specialized agents across 10 business use cases (example: 5 QC processes × 5 agents per process = 25 agents)
- **1,000 Invocations per Agent**: Each agent is invoked 1,000 times monthly to process workloads (e.g., 1,000 QC items per agent)
- **Total Monthly Sessions**: 25 agents × 1,000 invocations = 25,000 sessions (conservative estimate; actual may vary)
- **Claude 3.5 Sonnet Selection**: Banking agents require advanced reasoning capabilities (score: 90+ on MMLU) for complex financial decisions and regulatory compliance
- **High Token Counts (6,000 input/800 output)**: Necessary to handle massive system prompts for complex regulatory guardrails (Basel III, CCAR, GDPR) and detailed tool schemas (API definitions) for core banking systems
- **95% I/O Wait Time**: Reflects the reality of legacy banking middleware latency in enterprise environments where agents wait for core banking system responses
- **90% Prompt Caching Hit Rate**: Accounts for the repetitive nature of massive, static compliance headers and regulatory checks that remain constant across invocations

This configuration ensures the agents are sophisticated enough to avoid "hallucinating" financial data while remaining cost-optimized for a multi-million-user customer base.

## Pricing Model Summary

### Core Pricing Principles
- **Consumption-based pricing**: Pay only for active resource consumption
- **No upfront commitments or minimum fees**
- **Per-second billing** for compute resources
- **No charges for pre-allocated resources**

### Detailed Cost Components

#### 1. AgentCore Runtime Costs
- **vCPU**: Charged per-second usage - represents the computing power allocated to your agent
- **Memory (GB)**: Charged per GB-hour - represents the RAM allocated to your agent
- **Gateway Operation Fees**: Additional charges for each agent invocation/request processed

#### 2. AgentCore Browser (if used)
- Cloud-based browser runtime for website interaction
- Pay only for active resource consumption (unlike traditional compute services)
- No charges for pre-allocated resources

#### 3. AgentCore Memory Storage
- **Managed session storage**: Currently in public preview (S3-backed)
- Stores agent conversation history, state, and temporary data
- Pricing subject to change before General Availability (GA)
- Note: Check latest documentation for updated pricing

#### 4. Model Usage Costs (CRITICAL COMPONENT)
- **Input Tokts**: Charged per 1,000 tokens - cost for processing user queries and system prompts
- **Output Tokens**: Charged per 1,000 tokens - cost for generating agent responses
- **Cache Reads**: Charged per 1,000 tokens - cost for reusing cached prompt sections
- **Cache Writes**: Charged per 1,000 tokens - cost for storing prompt sections in cache
- **For Claude 3.5 Sonnet**: $0.003 per 1,000 input tokens, $0.015 per 1,000 output tokens

#### 5. Additional Potential Costs
- **Knowledge Retrieval**: Amazon Bedrock RAG retrieval costs apply if using knowledge base integration
- **Tool-Related Charges**: Separate charges for tools used by agents (AWS Lambda, third-party APIs, etc.)
- **Data Transfer**: Standard EC2 network data transfer rates apply
- **Guardrails**: Content filters metered daily, billed monthly (from Feb 1, 2025)
- **Agent Runtime**: Base compute costs for agent orchestration and management

## Detailed Cost Calculation for Tier 1 US-Based Retail Bank

Based on the AWS Calculator estimation and the specified parameters, here is the detailed monthly cost breakdown:

### Usage Parameters
- **Number of Agents**: 25 (5 QC processes × 5 agents per process)
- **Invocations per Agent**: 1,000 times monthly
- **Total Monthly Sessions**: 25,000 (25 agents × 1,000 invocations)
- **Model**: Claude 3.5 Sonnet
- **Input Tokens per Session**: 6,000
- **Output Tokens per Session**: 800
- **Prompt Cache Hit Rate**: 90%
- **I/O Wait Time**: 95%

### Monthly Cost Breakdown

#### 1. Model Usage Costs (Claude 3.5 Sonnet)
- **Input Tokens**: 25,000 sessions × 6,000 tokens = 150,000,000 tokens
  - At 90% cache hit rate: 15,000,000 tokens billed (10% uncached)
  - Cost: (15,000,000 ÷ 1,000) × $0.003 = $45.00
- **Output Tokens**: 25,000 sessions × 800 tokens = 20,000,000 tokens
  - Cost: (20,000,000 ÷ 1,000) × $0.015 = $300.00
- **Cache Writes**: 150,000,000 tokens × 90% = 135,000,000 tokens
  - Cost: (135,000,000 ÷ 1,000) × $0.003 = $405.00

#### 2. AgentCore Runtime Costs
- **vCPU**: Based on AWS Calculator estimation for specified configuration
- **Memory (GB)**: Based on AWS Calculator estimation for specified configuration
- **Gateway Operation Fees**: 25,000 invocations × fee per invocation

#### 3. Additional Cost Components
- **AgentCore Memory Storage**: Session storage for 25,000 monthly interactions
- **Data Transfer**: Standard EC2 rates for agent-core banking system communication
- **Guardrails**: Content filtering for financial compliance checks

### Total Estimated Monthly Cost
Based on the AWS Calculator reference (https://calculator.aws/#/estimate?id=19c2da06e479d0010c03861ae06628c18f94d044), the total estimated monthly cost for running 25 agents with the specified usage pattern can be calculated by summing the components above.

### Component Cost Summary

#### Model Usage Costs (from calculations above):
- Input Tokens: $45.00
- Output Tokens: $300.00
- Cache Writes: $405.00
- **Subtotal Model Usage**: $750.00

#### AgentCore Runtime Costs:
- vCPU, Memory, and Gateway Operation Fees: [VALUE FROM CALCULATOR - MODEL USAGE]

#### Additional Costs:
- AgentCore Memory Storage: [ESTIMATED BASED ON USAGE]
- Data Transfer: [BASED ON NETWORK USAGE]
- Guardrails: [BASED ON CONTENT FILTERING USAGE]

### Cost Per Transaction Calculations
- **Cost per Agent Invocation**: Total monthly cost ÷ 25,000 sessions
- **Cost per QC Process**: Cost per agent × 5 agents
- **Cost per 1,000 QC Items**: Cost per QC process × 5 processes

This breakdown provides transparency for executive stakeholders to understand exactly what drives the cost of running AI agents in a regulated banking environment, separating model usage costs from platform and operational costs.

## Comparison Considerations
### AgentCore vs Self-Hosting
- **AgentCore Advantages**:
  - No infrastructure management overhead
  - Automatic scaling
  - Built-in monitoring and multi-agent communication
  - Compliance and governance features
  - Pay-as-you-go model

- **Self-Hosting Considerations**:
  - Potential cost savings at scale
  - Full control over environment
  - Requires DevOps expertise for management
  - Need to handle scaling, monitoring, and updates manually

## Enterprise Deployment Factors

### Cost Optimization Strategies
1. **Right-sizing**: Monitor actual vCPU and GB-hour usage to optimize allocations
2. **Tool Selection**: Choose cost-effective tools for agent operations
3. **Knowledge Base Usage**: Optimize RAG queries to minimize retrieval costs
4. **Model Selection**: Choose appropriate model sizes for tasks
5. **Caching Strategies**: Implement caching for repeated operations
6. **Batch Processing**: Where applicable, batch operations to reduce gateway fees

### Hidden Costs to Watch For
1. **Data Transfer Charges**: Especially for agents accessing external APIs or large datasets
2. **Tool Execution Costs**: Lambda functions or third-party API calls can accumulate
3. **Memory Storage Growth**: Session storage costs can increase with usage
4. **Model Inference Costs**: If using custom models, separate inference charges apply
5. **Development and Testing**: Iterative development can increase costs during prototyping

## Recommendations for Enterprise Evaluation

### 1. Proof of Concept Approach
- Start with minimal viable agent to understand baseline costs
- Use AWS pricing calculator for initial estimates
- Monitor actual usage patterns for 4-6 weeks
- Scale gradually while tracking cost per transaction

### 2. Cost Monitoring Implementation
- Set up AWS Cost Explorer budgets and alerts
- Implement custom metrics for cost-per-agent-operation
- Tag resources appropriately for detailed cost allocation
- Regular review of usage patterns and optimization opportunities

### 3. Security and Compliance Considerations
- Factor in costs for:
  - VPC endpoints (if needed for private connectivity)
  - Encryption and key management (KMS)
  - Audit logging (CloudTrail, Config)
  - IAM roles and policies management

## Limitations of Current Information
- Pricing details may have changed since sources were published
- Managed session storage pricing is still in preview and subject to change
- Enterprise volume discounts not visible in public pricing
- Regional pricing variations may apply

## Next Steps for Detailed Analysis
1. **Official AWS Pricing Calculator**: Use AWS official tools for account-specific estimates
2. **Pilot Program**: Run a limited enterprise pilot with detailed cost tracking
3. **Usage Modeling**: Develop models based on expected agent interaction patterns
4. **Comparative Analysis**: Compare against alternative platforms (Azure AI Services, Google Vertex AI Agent Builder, etc.)
5. **ROI Modeling**: Factor in development speed, operational overhead reduction, and time-to-market benefits

## References
1. Amazon Bedrock AgentCore Pricing - AWS (aws.amazon.com/bedrock/agentcore/pricing/)
2. Amazon Bedrock AgentCore - AWS (aws.amazon.com/bedrock/agentcore/)
3. Overview - Amazon Bedrock AgentCore (docs.aws.amazon.com/bedrock-agentcore/latest/devguide/what-is-bedrock-agentcore.html)
4. AgentCore (Bedrock) Pricing and When Self-Hosting Wins (scalevise.com/resources/agentcore-bedrock-pricing-self-hosting/)
5. AWS Bedrock Pricing: Hidden Costs, Real Savings - Medium (cloudshim.medium.com/aws-bedrock-pricing-hidden-costs-real-savings-4dd350aee41e)
6. Predicting Your AI Agent's Cost - DEV Community (dev.to/aws/predicting-your-ai-agents-cost-6m9)
7. Amazon Bedrock AgentCore: Pricing, Features & How It Works (www.pump.co/blog/amazon-bedrock-agentcore)
8. Inquiry about Model Usage Costs for AgentCore Memory | AWS re:Post (repost.aws/questions/QUc6dELuZwRhidwCRV4kFSgA/inquiry-about-model-usage-costs-for-agentcore-memory)

---
*Analysis conducted: 2026-04-19*
*Sources: Public web search results, AWS documentation, technical blogs, and community discussions*
*Note: For enterprise procurement, always verify with official AWS pricing documents and consult with AWS representatives for contract-specific pricing*

---

## Review (added 2026-04-21)

The document is a solid starting point for scoping AgentCore costs in a regulated enterprise, but it reads more like a framework than a finished estimate. A decision-maker handed this today would not be able to answer "what will it cost us per month?" The gaps are fixable, and several numbers should be reworked before the analysis is shared beyond an internal working group.

### Accuracy and math

The token-cost arithmetic on the model-usage section is internally consistent but misrepresents how Bedrock/Anthropic prompt caching is actually billed, which is the largest line item in the breakdown:

- **Cache writes are priced differently from input tokens.** The document applies the $0.003/1K input-token rate to 135M "cache write" tokens to get $405. On Anthropic's published pricing for Claude 3.5 Sonnet, cache writes are ~$0.00375/1K (a 25% premium over input), and on Bedrock prompt caching the write premium is similar. Using the correct rate, the same 135M tokens is closer to $506, not $405.
- **Cache reads are missing entirely.** With a 90% hit rate, the bulk of input — 135M tokens/month — are cache reads, which are the cheap side of the ledger (~$0.0003/1K, roughly 10% of input). That's ~$40/month and deserves its own line; omitting it makes the cached architecture look more expensive than it is relative to the uncached baseline.
- **Cache writes should not scale with every invocation.** The assumption that 90% of every session's 6,000 input tokens is written to cache each time contradicts how prompt caching works — you write once, then read on subsequent hits within the TTL (5 min default). Unless the system prompt truly evicts and rewrites every request, cache-write volume should be a function of cache TTL and session concurrency, not session count × hit rate. This likely overstates cache-write cost by 1–2 orders of magnitude.

Net: the $750 model-usage subtotal is probably high by a factor of ~3–5x because of how cache writes are modeled. Reworking this is the highest-value edit.

### Completeness

Several components are listed as placeholders that make the "Total Estimated Monthly Cost" unusable as-is:

- vCPU, memory (GB-hour), and gateway operation fees are labeled "Based on AWS Calculator estimation" with no actual numbers. Since the linked calculator estimate is the source of truth, pulling those values in is table-stakes.
- AgentCore Memory Storage, Data Transfer, and Guardrails are all marked "[ESTIMATED]" / "[BASED ON ...]" with no estimate. Even rough ranges (e.g., "Guardrails: ~$0.15/1K text units, ~$X/month at this volume") would give the reader something to compare.
- Cost-per-transaction formulas are written but not evaluated. For an executive audience, the punch line — dollars per invocation, dollars per QC item — is the number they'll remember. Leaving it as a formula undercuts the document's purpose.

### Assumptions worth challenging

- **95% I/O wait time** is called out as a justification but doesn't show up in the math. On AgentCore, vCPU is billed per second of active use, so high I/O wait should *reduce* compute cost — but only if the runtime actually releases the vCPU during waits, which depends on implementation. Worth stating explicitly whether the 95% figure is being modeled as cost-saving, cost-neutral, or cost-additive.
- **90% cache hit rate** is optimistic for agentic workloads with variable tool-call paths. Typical production numbers I've seen cited are 50–70%. Including a sensitivity table (hit rate 50% / 70% / 90%) would make the estimate more defensible.
- **Claude 3.5 Sonnet** is the modeled choice, but as of April 2026 newer Claude models (Sonnet 4.6, Haiku 4.5) are available on Bedrock with different price/performance profiles. For a banking use case, it's worth either justifying sticking with 3.5 Sonnet or modeling Haiku 4.5 as a lower-cost tier for simpler agents in the pipeline.

### Minor issues

- Line 47: typo — "Input Tokts" should be "Input Tokens."
- The "Cost Per Transaction Calculations" section multiplies 5 agents × 5 processes to reach 1,000 QC items, but the setup said 1,000 invocations per agent and 25 agents total. The per-1,000-items math doesn't cleanly derive from the stated parameters — reconcile or restate.
- References list URLs as bare text rather than markdown links, which is fine, but several are third-party blogs (Medium, DEV, scalevise) cited alongside AWS primary docs. Marking which are authoritative vs. secondary would help readers assess reliability.
- "Managed session storage is in public preview" is noted but not dated. In a fast-moving pricing area, noting "as of [date] per [URL]" prevents stale claims from outliving their shelf life.

### What to add

- A concrete total-monthly-cost number (even a range) by pulling the calculator values in.
- A sensitivity table across 2–3 cache hit rates and 2–3 model choices.
- A separate "what would self-hosting cost at this scale" comparison, since the doc frames AgentCore vs. self-hosting qualitatively but doesn't quantify the break-even point — that's the single question most enterprise buyers will ask.
- A 12-month projection showing how the cost scales if agents or invocations grow 2x/4x — useful for budgeting conversations.

### Overall

The framing, use-case justification (Tier 1 bank, regulatory load, legacy middleware latency), and cost-component taxonomy are strong and would be hard to reproduce from scratch. The work that remains is largely quantitative: correct the caching math, fill in calculator values, and produce the summary numbers the current draft gestures at. With those changes, this becomes a credible input to a procurement conversation rather than a scaffold.

*Reviewer: Claude (automated review)*  
*Review date: 2026-04-21*