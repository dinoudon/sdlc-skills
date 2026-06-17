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

Build production-grade AI/LLM systems. Covers LLMOps, prompt engineering, model integration, AI safety, and production deployment patterns.

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

### Key Concepts

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

AI Safety:
  - Guardrails and content filtering
  - Bias detection and mitigation
  - Hallucination prevention
  - Responsible AI practices

Production AI:
  - Scaling inference
  - Cost optimization
  - Latency management
  - Reliability and fallbacks
```

---

## Step 1: Prompt Engineering Fundamentals

### Prompt Structure

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
  - Provides example output
  - Constrains response length
```

### Prompting Techniques

```
Zero-shot:
  - No examples provided
  - Relies on model's pre-training
  - Best for simple, well-defined tasks
  
  Example:
    "Classify this review as positive, negative, or neutral:
     'The product is amazing and works perfectly.'"

Few-shot:
  - Provides examples before the task
  - Helps model understand pattern
  - Best for specific output formats
  
  Example:
    "Classify reviews:
     Review: 'Great product!' → Positive
     Review: 'Terrible experience.' → Negative
     Review: 'It works fine.' → Neutral
     
     Now classify: 'The product is amazing.'"

Chain-of-thought:
  - Asks model to show reasoning
  - Improves complex reasoning tasks
  - Reduces hallucination
  
  Example:
    "Think step by step:
     1. What is the problem?
     2. What information do I have?
     3. What is the solution?
     
     Problem: If I have 3 apples and give away 1, how many do I have?"

Role prompting:
  - Assigns specific role to model
  - Sets expertise and perspective
  - Best for domain-specific tasks
  
  Example:
    "You are a senior software engineer with 15 years of experience.
     Review this code for security vulnerabilities."
```

### Prompt Optimization

```
Iteration process:
  1. Start with simple prompt
  2. Test with diverse inputs
  3. Identify failure modes
  4. Add constraints or examples
  5. Test again and measure
  6. Repeat until satisfactory

Common issues and fixes:
  - Too verbose: Add "Be concise" or word limit
  - Wrong format: Add explicit format instructions
  - Hallucination: Add "Only use provided context"
  - Inconsistent: Add examples or templates
  - Too creative: Add "Stick to facts"
  - Too conservative: Add "Think creatively"

Model-specific tips:
  GPT-4/4o:
    - Responds well to system prompts
    - Good at following complex instructions
    - Prefers structured prompts
  
  Claude:
    - Excellent at long context
    - Responds to role prompting
    - Prefers clear, detailed instructions
  
  Gemini:
    - Good at multimodal tasks
    - Responds to few-shot examples
    - Prefers concise prompts
  
  Llama/Mistral:
    - Better with simpler prompts
    - Few-shot helps significantly
    - May need more explicit instructions
```

---

## Step 2: LLMOps Architecture

### System Architecture

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
    - Separate services per function
    - Independent scaling
    - More complex to manage
  
  Multi-model:
    - Different models for different tasks
    - Cost optimization
    - Requires orchestration

Example architecture:
  ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
  │  Frontend   │────▶│  API Gateway │────▶│  LLM Router │
  └─────────────┘     └─────────────┘     └─────────────┘
                                                 │
                    ┌────────────────────────────┼────────────────────────────┐
                    │                            │                            │
              ┌─────▼─────┐              ┌──────▼──────┐              ┌──────▼──────┐
              │  GPT-4    │              │  Claude     │              │  Llama      │
              │  (complex)│              │  (long ctx) │              │  (simple)   │
              └───────────┘              └─────────────┘              └─────────────┘
```

### Model Selection

```
Factors to consider:
  - Task complexity
  - Context length requirements
  - Latency requirements
  - Cost constraints
  - Accuracy requirements
  - Safety requirements

Model tiers:
  Tier 1 (highest capability):
    - GPT-4o, Claude Opus, Gemini Ultra
    - Complex reasoning, long context
    - Highest cost, highest quality
  
  Tier 2 (balanced):
    - GPT-4o-mini, Claude Sonnet, Gemini Pro
    - Good balance of cost and quality
    - Most production use cases
  
  Tier 3 (fast/cheap):
    - GPT-3.5-turbo, Claude Haiku, Gemini Flash
    - Simple tasks, high volume
    - Lowest cost, fastest response

Selection matrix:
  Task Type          → Recommended Tier
  Complex reasoning  → Tier 1
  Code generation    → Tier 1 or 2
  Summarization      → Tier 2
  Classification     → Tier 2 or 3
  Simple Q&A         → Tier 3
  High-volume batch  → Tier 3
```

---

## Step 3: RAG (Retrieval-Augmented Generation)

### RAG Architecture

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
  7. Combine query + retrieved context
  8. Send to LLM for generation
```

### Chunking Strategies

```
Fixed-size chunking:
  - Split by character/token count
  - Simple and predictable
  - May break semantic units
  
  Parameters:
    chunk_size: 500-1000 tokens
    chunk_overlap: 50-100 tokens

Recursive chunking:
  - Split by separators (paragraph, sentence)
  - Preserves semantic units
  - Better for structured text
  
  Separators: ["\n\n", "\n", ". ", " "]

Semantic chunking:
  - Split by topic/meaning
  - Uses embeddings to detect boundaries
  - Best quality but slower
  
  Approach:
    1. Generate embeddings for each sentence
    2. Calculate similarity between consecutive sentences
    3. Split where similarity drops significantly

Document-specific:
  - Markdown: Split by headers
  - Code: Split by functions/classes
  - HTML: Split by DOM structure
  - PDF: Split by pages or sections
```

### Vector Databases

```
Options:
  Pinecone:
    - Managed service
    - Easy to use
    - Good for production
  
  Weaviate:
    - Open-source
    - Rich querying
    - Good for complex use cases
  
  Qdrant:
    - Open-source
    - High performance
    - Good for large-scale
  
  Chroma:
    - Open-source
    - Simple API
    - Good for prototyping
  
  pgvector:
    - PostgreSQL extension
    - Existing infrastructure
    - Good for hybrid workloads

Selection criteria:
  - Scale (vectors, queries/sec)
  - Query capabilities (filtering, hybrid)
  - Deployment (managed vs self-hosted)
  - Cost
  - Integration with existing stack
```

---

## Step 4: Function Calling & Tool Use

### Function Calling

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
    {
      "type": "function",
      "function": {
        "name": "get_weather",
        "description": "Get current weather for a location",
        "parameters": {
          "type": "object",
          "properties": {
            "location": {
              "type": "string",
              "description": "City name"
            }
          },
          "required": ["location"]
        }
      }
    }
  ]
```

### Tool Use Patterns

```
Single tool:
  - LLM calls one tool
  - Simple and predictable
  - Most common pattern

Sequential tools:
  - LLM calls tools in sequence
  - Each call depends on previous result
  - Example: search → analyze → summarize

Parallel tools:
  - LLM calls multiple tools simultaneously
  - Independent operations
  - Example: get weather for multiple cities

Conditional tools:
  - LLM decides which tool to call
  - Based on query analysis
  - Example: route to different APIs

Tool selection:
  - Provide clear tool descriptions
  - Include parameter schemas
  - Add examples in system prompt
  - Limit number of tools (5-10 max)
```

---

## Step 5: AI Safety & Guardrails

### Content Filtering

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
        # Check for harmful content
        if self.contains_harmful(prompt):
            return Block("Harmful content detected")
        # Check for injection attempts
        if self.is_injection(prompt):
            return Block("Injection attempt detected")
        return Allow()
    
    def check_output(self, response):
        # Check for harmful content
        if self.contains_harmful(response):
            return Block("Harmful content in response")
        # Check for hallucinations
        if self.is_hallucination(response):
            return Warn("Possible hallucination")
        return Allow()
```

### Hallucination Prevention

```
Techniques:
  1. Grounding:
     - Provide explicit context
     - Use RAG for factual retrieval
     - Cite sources in response
  
  2. Temperature control:
     - Lower temperature (0.0-0.3) for factual tasks
     - Higher temperature (0.7-1.0) for creative tasks
  
  3. Verification:
     - Ask model to cite sources
     - Request confidence scores
     - Implement fact-checking pipeline
  
  4. Constraints:
     - "Only use provided context"
     - "If unsure, say 'I don't know'"
     - "Do not make up information"

Detection:
  - Compare response to source material
  - Check for factual consistency
  - Verify citations exist and are accurate
  - Use separate model for verification
```

---

## Step 6: Production Deployment

### Scaling Strategies

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
  
  When to use:
    - Model requires more VRAM
    - Latency critical
    - Batch processing

Caching:
  - Response caching
  - Embedding caching
  - Prompt caching
  
  Strategies:
    - Exact match caching
    - Semantic similarity caching
    - TTL-based expiration
    - LRU eviction

Cost optimization:
  - Model selection (use cheapest that works)
  - Prompt optimization (shorter prompts)
  - Caching (avoid redundant calls)
  - Batching (process multiple requests)
  - Quantization (reduce model precision)
```

### Monitoring & Observability

```
Key metrics:
  - Latency (p50, p95, p99)
  - Throughput (requests/second)
  - Error rate
  - Cost per request
  - Token usage (input/output)
  - Cache hit rate
  - Model accuracy/quality

Quality metrics:
  - Relevance score
  - Faithfulness score
  - Answer correctness
  - User satisfaction
  - Task completion rate

Tools:
  - LangSmith (LLM observability)
  - Weights & Biases (experiment tracking)
  - Phoenix (LLM tracing)
  - Helicone (LLM analytics)
  - Custom dashboards (Grafana, Datadog)

Alerting:
  - Latency spikes
  - Error rate increases
  - Cost anomalies
  - Quality degradation
  - Rate limit hits
```

---

## Step 7: Evaluation & Testing

### Evaluation Framework

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
     - Completeness: Does it cover all aspects?
     - Clarity: Is it easy to understand?
     
     Question: {question}
     Response: {response}
     Reference: {reference}"

Human evaluation:
  - Expert review
  - User feedback
  - A/B testing
  - Crowdsourced evaluation

Test categories:
  - Unit tests (individual components)
  - Integration tests (end-to-end)
  - Regression tests (no quality degradation)
  - Stress tests (under load)
  - Safety tests (guardrails work)
```

### A/B Testing

```
Setup:
  1. Define hypothesis
  2. Create control and treatment
  3. Split traffic (50/50 or gradual)
  4. Collect metrics
  5. Analyze results
  6. Make decision

Metrics to track:
  - Task completion rate
  - User satisfaction
  - Latency
  - Cost
  - Error rate

Statistical significance:
  - Minimum sample size
  - Confidence interval (95%)
  - Effect size
  - P-value

Example:
  Control: GPT-4o with basic prompt
  Treatment: GPT-4o with optimized prompt
  
  Hypothesis: Optimized prompt improves task completion by 10%
  
  Results:
    Control: 75% completion
    Treatment: 82% completion
    P-value: 0.03 (significant)
    Decision: Ship treatment
```

---

## Step 8: Cost Optimization

### Cost Management

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
     - Use system prompts efficiently
  
  3. Caching:
     - Cache common queries
     - Use semantic caching
     - Set appropriate TTL
  
  4. Batching:
     - Process multiple requests together
     - Reduce API call overhead
     - Use async processing
  
  5. Streaming:
     - Stream responses to reduce time-to-first-token
     - Better user experience
     - Same cost, better perceived performance

Cost monitoring:
  - Track cost per request
  - Set budget alerts
  - Monitor cost trends
  - Identify expensive queries
  - Optimize high-volume endpoints
```

### Token Optimization

```
Token counting:
  - 1 token ≈ 4 characters (English)
  - 1 token ≈ 0.75 words
  - Different for other languages

Reduction techniques:
  - Remove whitespace
  - Use abbreviations where appropriate
  - Compress context
  - Summarize long inputs
  - Use shorter system prompts

Example:
  Before: "Please analyze the following text and provide a summary 
           that covers the main points and key takeaways."
  After:  "Summarize main points and takeaways."
  
  Savings: ~20 tokens per request
  At 1M requests/month: ~20M tokens saved
```

---

## Step 9: AI Engineering Patterns

### Common Patterns

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
  - Use for complex transformations

Router Pattern:
  - Classify input
  - Route to appropriate handler
  - Use for multi-domain systems

Evaluator Pattern:
  - Generate response
  - Evaluate quality
  - Regenerate if poor
  - Use for high-quality requirements

Fallback Pattern:
  - Try primary model
  - If fails, try secondary
  - If fails, use default
  - Use for reliability
```

### Anti-Patterns

```
Prompt stuffing:
  - Too much context in prompt
  - Confuses model
  - Increases cost
  - Fix: Summarize or use RAG

Over-reliance on LLM:
  - Using LLM for simple tasks
  - Unnecessary cost and latency
  - Fix: Use deterministic solutions when possible

No error handling:
  - Assuming LLM always works
  - No fallbacks
  - Fix: Implement retry, fallback, validation

Ignoring safety:
  - No content filtering
  - No bias detection
  - Fix: Implement guardrails

No monitoring:
  - No visibility into performance
  - Can't detect issues
  - Fix: Implement observability
```

---

## Step 10: Tools & Frameworks

### LLM Frameworks

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
  - .NET and Python
  - Good for Azure integration

Haystack:
  - NLP framework
  - Pipeline-based
  - Production-ready
  - Good for search/RAG

DSPy:
  - Programming framework
  - Optimizes prompts automatically
  - Research-focused
  - Good for optimization
```

### Model Serving

```
vLLM:
  - High-performance serving
  - PagedAttention
  - Continuous batching
  - Good for self-hosted

TGI (Text Generation Inference):
  - HuggingFace's serving solution
  - Easy to deploy
  - Good for HuggingFace models

Ollama:
  - Local model serving
  - Simple setup
  - Good for development

LiteLLM:
  - Unified API for 100+ LLMs
  - Proxy server
  - Good for multi-model

OpenRouter:
  - API gateway for multiple providers
  - Fallback and routing
  - Good for production
```

---

## Related Skills

- [sdlc-observability](sdlc-observability): Monitoring and observability for AI systems
- [sdlc-testing-qa](sdlc-testing-qa): Testing strategies for AI applications
- [sdlc-architecture-design](sdlc-architecture-design): System design for AI systems
- [sdlc-deployment](sdlc-deployment): Deployment patterns for AI services
