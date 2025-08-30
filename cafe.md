# AI/DOC-FIRST MONOREPO GUIDE & PROMPT

## Philosophy
- Humans focus on ideas, architecture, and documentation
- AI handles implementation, repetitive tasks, pipelines, testing, and deployment
- Everything starts with documentation: doc-first approach
  - Each silo has a README.md describing purpose, APIs, dependencies, build/test/deploy
  - New features start with a doc under /silo/x/docs/features/
  - AI uses docs as context to generate code/pipelines/tests

## Repo Structure
- /base -> shared framework libraries
- .cafe/actions/ -> shared actions (build/test/lint/ci/deploy)
- /silo/x/ -> independent services, using base
- /silo/x/.cafe/actions/ -> silo-specific actions (override/extend shared ones)
- /pipelines/templates/ -> shared Azure DevOps pipeline templates
- /patterns/ -> reusable implementation patterns (retry, logging, metrics)
- /docs/knowledge/ -> FAQs, fixes, learnings
- /docs/postmortems/ -> incident reviews

## Actions & Agents
- File named {ACTION}.md defines a action
- Actions define: purpose, invocation, expected outputs, AI notes
- AI can call actions (like "agents") to build, test, lint, deploy
- Shared actions, e.g. build.md, test.md, deploy.md, lint.md, ci.md
- Silo-specific actions: add new ones or override shared actions with local nuances
- Enforce schema for actions to ensure consistency
  (e.g. yaml with fields like `name`, `description`, `inputs`, `outputs`, `ai_notes`)

## Toolchain Integration

### Integration Points
Each toolchain typically define their own customizatioin points (e.g. .github/instructions.md for GitHub Copilot, etc).
Those integration points should reference main README.md to ensure consistent rules across toolchains.

### Context Scope
- CLI (aider-like): scope is set by current directory (silo context)
- GUI (Roo Code): configure per-folder context rules
- Both approaches limit AI scope to relevant silo + root rules

## Instructions
- Root README.md defines directly or links to various files for:
  - Shared conventions (coding style, etc)
  - Document-first rule
  - Azure DevOps pipelines must extend from templates
  - Shared actions + per-silo overrides
  - Safety rules (no secrets, no bypass of approvals)
- Per-silo README.md adds service-specific rules

## Culture & Governance
- PR template enforces doc-first updates (README/FEATURE + actions + pipeline)
- All AI-generated PRs labeled `[ai-generated]` and reviewed
- AI must never log secrets or disable TLS
- Knowledge sharing through /patterns/, /docs/knowledge/, /docs/postmortems/

## Silo Autonomy via AI
- Each sile has its own playbook of actions, pipelines, and docs
- AI can fully operate within a silo's context over its lifecycle (build, test, deploy, doc generation)
- Developers steer at the architecture and documentation level

## Workflow Example
1. Developer writes README.md or feature doc
   (starting with draft PR to provide stage for discussion and team input)
2. AI reads doc, generates code/tests/pipeline changes
3. Developer reviews/polishes AI output
4. Commands ensure consistent build/test/deploy
5. CI/CD pipelines (ADO) run shared templates with silo extensions
   (can be triggered by AI using az-cli devops plugin, described in actions)
6. Governance ensures docs, actions, and pipelines stay in sync

## Sandboxed Experimentation
- Create `playground` silo for testing new ideas where AI can scaffold
  experimental code, pipelines, actions and docs without polluting main silos
- Developers can use this to refactor useful pieces for features, patterns, or actions

## AI Notes (Meta)
- Always prefer clarity and standardization over silo drift
- Always link doc-first, actions, and pipelines
- If silo has no override, fall back to shared defaults
- Use /patterns/ before re-inventing logic
