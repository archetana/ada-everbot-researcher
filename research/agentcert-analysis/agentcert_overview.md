# AgentCert Platform Overview: Features and Architecture for Autonomous AI Agent Research

## Abstract
AgentCert is an open-source chaos engineering platform extended with AI agent capabilities for autonomous fault detection, diagnosis, and remediation in Kubernetes environments. This document summarizes its key features, architecture, and research-relevant components for literature review and potential academic contributions.

## 1. Platform Overview
AgentCert combines:
- **Chaos Engineering** (via LitmusChaos) for controlled fault injection
- **AI Agent Orchestration** for autonomous monitoring and remediation
- **LLM Observability** (Langfuse integration) for agent behavior tracing
- **Unified API Gateway** (LiteLLM) for LLM provider abstraction
- **Hub-Based Discovery** (AgentHub/AppsHub) for agent and application cataloging

## 2. Core Architecture Components

### 2.1 Control Plane Services
- **Authentication Service** (Go): JWT-based auth, gRPC & REST APIs (ports 3000/3030)
- **GraphQL Server** (Go): Core API layer, integrates with MongoDB and Auth Service (ports 8080/8082)
- **Frontend** (React/TypeScript): Web UI for experiment management and hub visualization (port 2001)

### 2.2 Data Plane & Observability
- **MongoDB**: Primary storage for platform state, experiment metadata, and agent registrations
- **Langfuse**: LLM observability platform capturing agent traces, metrics, and scoring
- **LiteLLM**: Unified proxy for LLM providers (OpenAPI-compatible) with Langfuse callbacks
- **Minikube/Kubernetes**: Local cluster for running chaos experiments and agent workloads

### 2.3 Extension Hubs (Key Research Contributions)
- **ChaosHub** (existing): Catalog of chaos faults from `chaos-charts` Git repository
- **AgentHub** (novel): Catalogue of AI agents from `agent-charts` with live deployment status
- **AppsHub** (novel): Catalogue of target applications from `app-charts` with microservice-level status

## 3. Data Model & Storage Innovations
### 3.1 MongoDB Collections
- `project`: Multi-tenancy root
- `environment`: Deployment targets linked to chaos infrastructures
- `agentRegistry`: Deployed AI agents with capabilities and endpoints
- `chaosExperiments` & `chaosExperimentRuns`: Experiment definitions and execution evidence
- **Combined Metrics Collection**: Stores both quantitative (K8s metrics) and qualitative (LLM reasoning) data per agent run

### 3.2 LLM Observability Integration
- Vector search enabled metrics collection (Atlas Vector Search)
- Dual storage: Quantitative (structured) + Qualitative (reasoning traces) extractions
- Session-based correlation between chaos experiments and LLM agent behaviors
- Metadata enrichment: Agent version, environment, test duration, operator info

## 4. Agent-Centric Features for Research
### 4.1 AgentHub Design
- **Discovery**: Reads `agents.chartserviceversion.yaml` from `agent-charts` Git repo
- **Live Status Enrichment**: Cross-references with Agent Registry DB for deployment status
- **Card Information**: 
  - Name, displayName, one-liner description
  - Version, capabilities array
  - Deployment status (DEPLOYED/AVAILABLE) with namespace and Helm release
- **UX Pattern**: Category-based sidebar → paginated agent cards with status badges

### 4.2 AppsHub Design
- **Discovery**: Reads `applications.chartserviceversion.yaml` from `app-charts` Git repo
- **Live Status**: Queries Kubernetes API for microservice-level deployment status
- **Card Information**:
  - Application name, description, version, target namespace
  - Microservices breakdown with running/desired replicas
  - Deployment status and running services ratio
- **Use Case**: Benchmarking AI agent remediation against realistic microservices stacks

### 4.3 LLM-Powered Agent Capabilities (Evidence from Codebase)
- **Flash Agent**: Log metrics extraction using LLM → Langfuse trace reporting
- **K8s Agent**: Fault detection and autonomous remediation
- **Configuration**: 
  - Environment variables for LLM provider selection (OPENAI_API_KEY)
  - Langfuse integration for trace ingestion
  - LiteLLM proxy for model abstraction
- **Metrics Model**: 
  - Quantitative: Namespace, deployment name, detection accuracy, fault targets
  - Qualitative: RAI check status, reasoning score, trajectory efficiency, observability score

## 5. Experimental Workflow & Validation
### 5.1 Chaos Experiment Lifecycle
1. Define experiment via ChaosHub (fault selection)
2. Select target application via AppsHub
3. Deploy AI agent via AgentHub (or bring external agent)
4. Run experiment with fault injection
5. Agent observes, diagnoses, and optionally remediates
6. Platform captures:
   - Chaos metrics (pod/service status, resiliency scores)
   - LLM traces (reasoning trajectories, tool usage)
   - Quantitative/qualitative extractions for evaluation

### 5.2 Validation Mechanisms
- **Resiliency Scoring**: Based on fault injection outcomes and recovery
- **LLM-as-Judge**: Qualitative scoring on agent reasoning (0-1 scale)
- **Benchmark Applications**: Sock Shop and other microservices for controlled evaluation
- **Trace Correlation**: Langfuse traces linked to experiment runs via session IDs

## 6. Research Opportunities & Contributions
### 6.1 Autonomous Agent Evaluation
- Framework for comparing agent reasoning traces across different LLMs
- Metrics for agent effectiveness: detection accuracy, remediation speed, false positive rate
- Longitudinal studies on agent behavior drift with Langfuse trace storage

### 6.2 Hub-Based Agent Ecosystem
- Standardized agent onboarding via chartserviceversion.yaml
- Discovery mechanisms for agent capabilities and compatibility
- Version-controlled agent distribution through GitOps practices

### 6.3 Chaos Engineering + AI Synergy
- Controlled fault injection as validation ground truth for agent detection
- Remediation effectiveness measurement through experiment reruns
- Failure mode analysis using combined chaos/LLM trace data

### 6.4 Observability & Explainability
- End-to-end traceability from fault injection → agent action → system recovery
- Vector search for similarity-based agent behavior clustering
- Audit trails for governance and compliance (SOC 2, PCI-DSS mapping referenced)

## 7. Technical Requirements for Reproduction
### 7.1 Infrastructure
- Local: Docker, Go 1.24+, Node.js 18+, Python 3.10+, Yarn
- Cluster: Minikube with 2 CPUs, 4GB RAM
- Services: MongoDB 5.0 (replica set), Postgres (for LiteLLM)

### 7.2 Key Repositories
- Platform: https://github.com/AgentCert/AgentCert
- Agent Charts: https://github.com/agentcert/agent-charts (referenced)
- App Charts: https://github.com/agentcert/app-charts (referenced)
- Chaos Charts: https://github.com/agentcert/chaos-charts (referenced)

### 7.3 Environment Variables (Critical for AI Features)
```bash
# LLM Observability
OPENAI_API_KEY="your-key"
LITELLM_URL="http://localhost:4000"
MODEL_ALIAS="gpt-4o"
LANGFUSE_PUBLIC_KEY="pk-lf-xxxxx"
LANGFUSE_SECRET_KEY="sk-lf-xxxxx"
LANGFUSE_HOST="https://cloud.langfuse.com"

# Agent Hubs (auto-discovered via Git)
DEFAULT_AGENT_HUB_GIT_URL="https://github.com/agentcert/agent-charts"
DEFAULT_APP_HUB_GIT_URL="https://github.com/agentcert/app-charts"
```

## 8. Conclusion
AgentCert provides a unique platform for researching autonomous AI agents in safety-critical infrastructure environments. Its combination of:
- Chaos engineering as a controlled validation ground truth
- Hub-based agent/application discovery with live status enrichment
- Unified LLM observability and metrics extraction
- Modular, extensible architecture following GitOps principles

makes it suitable for studies in:
- Autonomous agent evaluation and benchmarking
- Explainable AI for infrastructure management
- Human-AI teaming in DevOps/SRE contexts
- LLM-powered reasoning trace analysis
- Chaos engineering as a service for AI validation

The platform's open-source nature and detailed documentation facilitate reproduction and extension for academic research.

---
*Summary compiled from AgentCert platform documentation, architecture diagrams, and source code review*
*Date: 2026-04-26*
*For: Autonomous AI Agent Research Literature Review*