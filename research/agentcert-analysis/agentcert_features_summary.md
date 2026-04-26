# AgentCert Platform Features Summary for AI Agent Research

## Overview
AgentCert is an open-source chaos engineering platform extended with AI agent capabilities for autonomous fault detection, diagnosis, and remediation in Kubernetes environments. It combines traditional chaos engineering with LLM-powered agents to create a closed-loop system for validating AI agent reliability in production-like scenarios.

## Core Architecture Components

### 1. Control Plane (Host-based Services)
- **Authentication Service (Go)**: Handles user auth, JWT tokens, gRPC on ports 3000/3030
- **GraphQL Server (Go)**: Core API layer serving REST/GraphQL on 8080, gRPC on 8082
- **Frontend (React)**: Web UI on port 2001 for visualization and control
- **LiteLLM Proxy**: Unified LLM gateway on port 4000 with OpenAI-compatible API
- **Langfuse Integration**: LLM observability for tracing agent behavior and performance

### 2. Data Plane (Minikube/Kubernetes)
- **Subscriber Pod**: Communicates with control plane to receive experiment configs
- **Chaos Operator/Runner**: Executes chaos experiments (pod-delete, network-latency, etc.)
- **Argo Workflow Controller**: Orchestrates complex multi-step experiments
- **Chaos Exporter**: Exports metrics for monitoring and analysis

### 3. Storage & Telemetry
- **MongoDB**: Primary datastore for platform state, experiment metadata, agent registry
- **Langfuse**: Distributed tracing and scoring for LLM agent interactions
- **PostgreSQL**: Backend for LiteLLM proxy (usage tracking, caching)

## Key Features for AI Agent Research

### A. AgentHub & AppsHub (Novel Contribution)
- **AgentHub**: Catalog of AI agents from `agent-charts` Git repo with live deployment status
  - Shows agent capabilities, versions, and real-time status (DEPLOYED/AVAILABLE)
  - Enables discovery and selection of agents for experimentation
- **AppsHub**: Catalog of target applications from `app-charts` Git repo with microservice-level status
  - Shows deployed applications, running services per microservice, and namespace info
  - Provides realistic benchmark targets for agent remediation capabilities

### B. LLM-native Agent Framework
- **LiteLLM Integration**: Unified access to multiple LLM providers (OpenAI, Azure, local)
- **Langfuse Observability**: Automatic tracing of agent reasoning, tool usage, and decision paths
- **Modular Design**: Agents can be developed as Helm charts with standardized interfaces
- **Vector Search**: MongoDB Atlas Vector Search for semantic retrieval of past experiments

### C. Closed-Loop Experimentation
- **Automated Workflow**: Define hypothesis → inject chaos → agent observes/acts → validate outcome
- **Resiliency Scoring**: Quantitative metrics (detection accuracy, fault targeting) + qualitative scores (RAI checks, reasoning quality)
- **Benchmarking Framework**: Compare agent performance across different LLMs, prompts, and agent architectures
- **Safety Mechanisms**: Guardrails, memory policies, and readiness patches to prevent runaway agents

### D. Production-Ready Infrastructure
- **Helm-based Deployment**: Consistent agent packaging and version control
- **Multi-tenancy**: Project/environment isolation for concurrent experiments
- **GitOps Integration**: Declarative agent/application definitions via Git repositories
- **Observability Stack**: Full tracing from user action → chaos injection → agent response → system recovery

## Research Opportunities Identified

### 1. Agent Reliability & Safety
- Study failure modes of LLM agents in chaotic environments
- Develop metrics for agent robustness beyond traditional accuracy
- Investigate prompt engineering strategies for consistent agent behavior under stress

### 2. Human-AI Collaboration in Incident Response
- Measure time-to-detection and time-to-mitigation with/without AI agents
- Study handoff effectiveness between autonomous agents and human operators
- Investigate trust calibration in agent-generated remediation suggestions

### 3. LLM Agent Architecture Optimization
- Compare different agent architectures (ReAct, Plan-and-Execute, etc.) for chaos engineering
- Study impact of context window size on agent performance in complex microservices
- Investigate retrieval-augmented generation (RAG) for incident-specific knowledge

### 4. Benchmarking Framework for Autonomous Agents
- Develop standardized chaos engineering benchmarks for agent evaluation
- Create difficulty curves based on fault type, blast radius, and detection complexity
- Establish baseline performance across different LLM families and sizes

### 5. Policy and Governance for Autonomous Systems
- Study effectiveness of RAI (Responsible AI) checks in preventing harmful agent actions
- Develop dynamic policy adjustment based on observed agent behavior
- Investigate audit trails and explainability for regulatory compliance in agent actions

## Connection to Current Research Objectives

This platform directly supports our research goals:
- **Autonomous Research**: Provides a testbed for experimenting with novel agent architectures
- **Scientific Writing**: Offers rich experimental data and clear evaluation metrics for publications
- **Production Coding**: Demonstrates MLOps/LLMOps practices in a real-world platform
- **Verification & Explainability**: Built-in mechanisms for validating agent behavior and tracing decisions
- **Local NVIDIA Hardware**: Can be configured to use local LLMs via LiteLLM for privacy-sensitive experiments

## References Within Repository
- `setup.md`: Complete local development guide
- `docs/AgentCert_Detailed_Data_Model.md`: Platform data model and storage architecture
- `docs/AGENTHUB_APPSHUB_DESIGN.md`: Design documents for AgentHub and AppsHub features
- `agentcert/`: Core agent implementation with LLM integrations
- `utils/mongodb_util.py`: MongoDB interface with vector search capabilities
- `utils/ai_search.py`, `utils/azure_openai_util.py`: LLM integration modules