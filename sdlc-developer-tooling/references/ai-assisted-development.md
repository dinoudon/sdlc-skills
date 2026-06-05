# AI-Assisted Development Best Practices

From katapultlabs/engineering-playbook. Rules for maximizing AI agent effectiveness in SDLC.

## Repository Preparation

### Agent Instruction Files
Create one of: `AGENTS.md`, `CLAUDE.md`, `.cursorrules`
Contents:
- Tech stack and versions
- Coding conventions (formatting, naming, imports)
- Architecture decisions and boundaries
- Testing strategy and commands
- Three-tier boundary rules:
  - **Always do**: follow conventions, write tests, use approved deps
  - **Human approval required**: schema changes, new dependencies, security-sensitive code
  - **Never touch**: production secrets, migration scripts in use, third-party vendored code

### Architecture Context
- Maintain ADRs (Architecture Decision Records) — why decisions were made
- Architecture overview: components, interactions, data flow, service boundaries
- Domain glossary: business terms → code representations
- PRDs as dependency-ordered, testable phases (not narrative prose)

### Executable Commands
- Document build/test/lint/deploy as copy-pasteable commands with full flags
- Testing strategy: framework, file locations, exact commands, coverage thresholds
- Security guardrails: approved dep sources, banned patterns (hardcoded creds, raw SQL)

### Environment Config
- `.aiignore` file: .env, secrets, credentials, sensitive data beyond .gitignore
- `llms.txt`: machine-readable project summary for LLM consumption
- Document cloud provider, CI/CD pipeline, env vars (without values), deploy targets

## Prompt Engineering for Code

### Concise Goal-Oriented (CGO) Prompting
- Emphasize functional objectives, not step-by-step instructions
- Research shows CGO produces fewer intermediate tokens while maintaining accuracy
- Cut 50-70% of prompt tokens with zero quality loss by eliminating filler

### File Inclusion Rules
- Include only files being modified — irrelevant files degrade output quality
- Use repo maps for structural awareness (file tree, dependency graph)
- Use diff-based editing — saves 31-98% output tokens vs full file regeneration

### Output Format
- TSV costs half the tokens of JSON
- Only use JSON when strict schema validation required
- Set max_tokens and stop sequences — output tokens cost 2-5x more than input

### Debugging Pattern
Progressive context expansion:
1. Error message first
2. Then the function
3. Then the file
Don't dump everything "just in case."

### Conversation Pattern
- Prefer single-turn interactions
- Multi-turn accumulates cost quadratically and degrades accuracy by up to 39%
- If multi-turn needed, summarize prior context in each turn

## Tests as Statement of Facts

- AI agents are complacent by default — they'll agree with your code even if it's wrong
- Test suites are the only mechanism that forces agents to respect actual business logic
- Write tests first (TDD), then let AI implement to satisfy them
- Tests define the contract; AI fills in the implementation

## Code for Agents (Not Just Humans)

- Explicit naming: `calculate_monthly_revenue` not `calc_rev`
- Small focused files: < 300 lines preferred
- Co-located intent comments: `// Why: we need to retry because the payment gateway has 30s timeouts`
- Consistent patterns: if one module uses repository pattern, all should
