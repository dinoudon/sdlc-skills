---
name: sdlc-ai-engineering
description: "AI/LLM engineering: LLMOps, prompt engineering, model integration, AI safety, production AI systems."
version: 6.3.0
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [ai, llm, mlops, llmops, prompt-engineering, ai-safety, model-integration, production-ai]
---

---
name: sdlc-ai-engineering
description: "AI/LLM engineering: LLMOps, prompt engineering, model integration, AI safety, production AI systems."
version: 6.3.0
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [ai, llm, mlops, llmops, prompt-engineering, ai-safety, model-integration, production-ai]
---

# AI Engineering Skill

Build production-grade AI/LLM systems. Covers LLMOps, prompt engineering, model integration, AI safety, and production deploy
## When to Use This Skill
- Building LLM-powered applications
- Integrating AI models into existing systems
- Optimizing prompt engineering workflows
- Implementing AI safety and guardrails
- Deploying models to production
- Monitoring and observability for AI systems
- Cost optimization for LLM inference
- Evaluating model performance
## Overview
AI engineering is the discipline of building reliable, scalable, and safe AI-powered systems. It combines software engineering best practices with ML/AI-specific concerns like prompt engineering, model evaluation, and AI safety.
```
LLMOps:
  - Operations for LLM-based systems
  - Similar to MLOps but focused on language models
  - Covers deployment, monitoring, cost management

Prompt Engineering:
  - Crafting inputs to get desired outputs from LLMs
  - Techniques: few-shot, chain-of-thought, role prompting
  - Model-specific optimizations

Model Integration:
  - Connecting LLMs to external systems
  - Function calling, tool use, RAG
  - Multi-model orchestration

```
## Step 1: Prompt Engineering Fundamentals
```
System prompt:
  - Sets model behavior and constraints
  - Defines role and persona
  - Establishes output format
  - Provides context and background

User prompt:
  - Specific task or question
  - Input data or context
  - Constraints and requirements
  - Desired output format

Assistant prompt (prefill):
  - Guides response format
  - Sets tone and style
```
## Step 2: LLMOps Architecture
```
Components:
  - Model serving (inference API)
  - Prompt management (versioning, templates)
  - Vector database (RAG, embeddings)
  - Cache layer (response caching)
  - Monitoring (latency, cost, quality)
  - Guardrails (content filtering, safety)

Architecture patterns:
  Monolithic:
    - Single model handles everything
    - Simple to deploy and manage
    - Limited scalability
    
  Microservices:
```
## Step 3: RAG (Retrieval-Augmented Generation)
```
Components:
  - Document loader (PDF, web, database)
  - Text splitter (chunking strategy)
  - Embedding model (text-to-vector)
  - Vector database (storage and search)
  - Retriever (similarity search)
  - LLM (generation with context)

Flow:
  1. Load documents
  2. Split into chunks
  3. Generate embeddings
  4. Store in vector DB
  5. User query → embed query
  6. Similarity search → retrieve relevant chunks
```
## Step 4: Function Calling & Tool Use
```
Definition:
  - LLM generates structured function calls
  - System executes functions
  - Results fed back to LLM
  - LLM generates final response

Use cases:
  - Database queries
  - API calls
  - Calculations
  - File operations
  - External service integration

Example (OpenAI format):
  tools = [
```
## Step 5: AI Safety & Guardrails
```
Input filtering:
  - Detect harmful prompts
  - Block injection attempts
  - Validate input format
  - Rate limiting

Output filtering:
  - Check for harmful content
  - Validate output format
  - Detect hallucinations
  - Ensure compliance

Implementation:
  class Guardrail:
    def check_input(self, prompt):
```
## Step 6: Production Deployment
```
Horizontal scaling:
  - Multiple model replicas
  - Load balancing
  - Auto-scaling based on traffic
  
  Implementation:
    - Kubernetes with HPA
    - Cloud provider auto-scaling
    - Queue-based scaling

Vertical scaling:
  - Larger model instances
  - GPU upgrades
  - Memory optimization
  
```
## Step 7: Evaluation & Testing
```
Automated evaluation:
  - Exact match (for classification)
  - BLEU/ROUGE (for generation)
  - BERTScore (semantic similarity)
  - Custom metrics (task-specific)

LLM-as-judge:
  - Use separate LLM to evaluate
  - Compare to reference answers
  - Score on multiple dimensions
  
  Example prompt:
    "Rate this response on a scale of 1-5:
     - Relevance: Does it answer the question?
     - Accuracy: Is the information correct?
```
## Step 8: Cost Optimization
```
Cost factors:
  - Model selection (GPT-4 vs GPT-3.5)
  - Token usage (input + output)
  - Request volume
  - Caching effectiveness

Optimization strategies:
  1. Model selection:
     - Use cheapest model that meets requirements
     - Route simple tasks to cheaper models
     - Reserve expensive models for complex tasks
  
  2. Prompt optimization:
     - Shorter prompts = fewer tokens
     - Remove redundant instructions
```
## Step 9: AI Engineering Patterns
```
RAG Pattern:
  - Retrieve relevant context
  - Augment prompt with context
  - Generate response
  - Use for knowledge-intensive tasks

Agent Pattern:
  - LLM as reasoning engine
  - Tools for actions
  - Memory for context
  - Use for complex, multi-step tasks

Chain Pattern:
  - Sequence of LLM calls
  - Each call processes previous output
```
## Step 10: Tools & Frameworks
```
LangChain:
  - Comprehensive LLM framework
  - Chains, agents, memory
  - Large ecosystem
  - Good for prototyping

LlamaIndex:
  - Data framework for LLMs
  - RAG specialization
  - Data connectors
  - Good for knowledge systems

Semantic Kernel:
  - Microsoft's LLM framework
  - Enterprise-focused
```
## Related Skills
- [sdlc-observability](sdlc-observability): Monitoring and observability for AI systems
- [sdlc-testing-qa](sdlc-testing-qa): Testing strategies for AI applications
- [sdlc-architecture-design](sdlc-architecture-design): System design for AI systems
- [sdlc-deployment](sdlc-deployment): Deployment patterns for AI services