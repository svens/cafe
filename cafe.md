# CAFE: Contextual Agent Framework Environment

[[_TOC_]]

## About This Framework

CAFE provides conventions and patterns for agent-human collaboration. Every concept is optional. Projects can adopt only the pieces that add value, ignore sections that don't fit their needs, customize patterns to match existing workflows, and mix CAFE with other approaches.

## Philosophy

- **Doc-first**: Documentation drives development, not the reverse
- **Human-agent collaboration**: Humans focus on ideas and architecture, agents handle implementation
- **Project freedom**: Minimal constraints, maximum flexibility in project organization
- **Context-aware**: Agents understand project structure through discoverable conventions

## Core Concepts

### Naming Conventions

CAFE uses standard names (`AGENTS.md`, `.agents/`, `README.md`) but all are examples only. Projects can choose alternatives that fit their conventions. Document chosen names in your agent entry point for discoverability.

### AGENTS.md

Entry point in project root about development and maintenance workflows.

Answers "how do I work on this project?":
- Development workflows (how to code, test, validate)
- Agent infrastructure (actions, roles, workflows - how to use them)
- Maintenance procedures (structural changes, discovery strategies)
- Git workflows and conventions
- Quality gates and validation steps

**Audience**: Primarily agents; humans reference for development workflows

**Resolution**: Located at project root. Defines project boundary for agent infrastructure. Monorepos can have multiple CAFE projects with separate AGENTS.md files.

### README.md

Project documentation about fundamentals and structure for general audience.

Answers "what is this project?":
- Project fundamentals (what it is, why it exists, what it does)
- Technology choices (languages, frameworks, protocols, specifications)
- Architecture and structure (components, layering, dependencies)
- Installation and setup (environment requirements)
- Basic tooling commands (build, test, format, lint commands)

**Audience**: Humans and agents (foundational knowledge)

**Resolution**: Cascading context hierarchy - walk up from current directory to project root. Parent provides general context, child READMEs inherit and add directory-specific details.

**Cross-referencing pattern**: AGENTS.md references README.md for project knowledge. README.md rarely references AGENTS.md (agents discover it via CAFE conventions).

### Actions

Actions are executable tasks that agents can invoke to build, test, deploy, or perform other project operations.

**Resolution**: Walk up from current directory to find `.agents/actions/ACTION_NAME` (executable file). Execute from discovery directory.

**Execution**: Actions can be any executable format (shell scripts, Python, etc.). Exit codes control workflow success/failure. stdout/stderr available to calling agent.

### Roles

Roles define how agents should interact within specific contexts through system prompts. Centralized at project root.

**Role Structure:**
- Stored in `.agents/roles/ROLE_NAME.md` (markdown file with agent prompt)
- Optional YAML frontmatter for configuration (temperature, model preferences)
- Can extend base roles using `extends: base_role_name` for composition

### Workflows

Workflows define agent-oriented series of steps to achieve complex goals like generating code from updated docs, reviewing changesets, or performing maintenance.

**Workflow Structure:**
- Stored in `.agents/workflows/WORKFLOW_NAME.md`
- Each step contains:
  - **Goal**: Short step objective
  - **Role**: Agent role to assume (see [Roles](#roles))
  - **Agent Prompt**: Prompt to achieve the goal
- Error handling: `on_failure: continue|stop|retry`, rollback actions
- Steps can pass context to subsequent steps (mechanism depends on agent platform)

**Resolution**: Walk up from current directory to find `.agents/workflows/WORKFLOW_NAME.md`. Execute from discovery directory.

**Execution**: Humans invoke via agent interface. Steps can switch roles. Interruptible/resumable.

## Documentation Principles

AI-generated documentation tends toward verbosity, causing humans to skip-read and miss critical details.

Enforce DRY/SPOT principles and fight verbosity:

**DRY (Don't Repeat Yourself)**:
- Every fact documented once, linked elsewhere
- Search before documenting to avoid duplication
- Context hierarchy determines ownership: parent-level concerns in parent docs, child-specific details in child docs

**SPOT (Single Point of Truth)**:
- Each document type owns specific content (see [README.md](#readme.md) and [AGENTS.md](#agents.md))
- Component READMEs: component-specific implementation details only

**Brevity**:
- Delete obvious explanations (basic tools, self-evident terms, standard practices)
- Trust agent competence - don't explain what agents already know
- Skip self-evident examples; for others, show the pattern, not exhaustive tutorials

## Project Structure

Projects organize themselves freely. CAFE conventions include:
- **Agent entry point** in project root (typically `AGENTS.md`) - orientation guide for agents
- `.agents/` directories for agent infrastructure (actions, roles, workflows)
- `README.md` files for contextual documentation
- No other prescribed folder layouts or naming conventions

**Multiple Projects in One Repository:**
Monorepos can have multiple CAFE projects, each with its own agent entry point defining a separate project root. Directory-walking stops at project boundaries.

## Workflow

1. **Document**: Write documentation describing goals, APIs, constraints
2. **Plan**: Break down work into tasks, identify dependencies, choose approaches
3. **Implement**: Agents generate code using documentation as context
4. **Validate**: Run actions (test, lint, build) to verify implementation
5. **Iterate**: Refine docs and code together

## TODO Management

CAFE does not prescribe TODO tracking. Projects can use any approach: file-based, external systems (GitHub Issues, Jira), or none.

**When useful**: Complex multi-step tasks, long-term projects, context preservation across sessions.

**Common approaches**:
- **File-based**: Discoverable location (`.agents/todos/`, `TODO/`, project root), human-readable format (markdown with metadata), action scripts for CRUD operations, delete completed tasks but preserve in git history
- **External systems**: CLI integration actions (e.g., `gh issue`, `az devops`), API wrappers for agent use
- **Hybrid**: File-based for agent working state, external system for team coordination

**Design considerations**: Storage location, organization (hierarchy vs flat, status tracking, priority), integration approach, scope/granularity appropriate to project scale.

## Managing Structural Changes

When project structure changes (adding/removing/renaming components, modules, or agent infrastructure), multiple files may need coordinated updates.

**Discovery Strategies**:
- Search-based: `rg "old_component_name"`
- Validation-enforced: build/test failures, linting, CI
- Sync markers: `SYNC:STRUCTURE` comments

**Pattern**: Plan (identify affected locations), Update, Validate (build/test/lint), Review (confirm completeness).

For projects with frequent structural changes, consider defining this as a workflow in `.agents/workflows/` (see [Workflows](#workflows)).

## Continuous Improvement

### Learning From Mistakes

Agent mistakes despite following docs/validations reveal opportunities to improve agent infrastructure.

**Categories**: CAFE gaps (missing patterns, unclear discovery) or project gaps (ambiguous guidance, missing validation).

**Out of scope**: Trivial issues (typos, minor inconsistencies).

### Root Cause Analysis

**Methods**:
- **5-Whys Method**: Ask "why" 3-5 times to reach root cause
- **Fishbone Analysis**: Examine People, Process, Documentation, Tools

### Lessons Learned

**Storage**: Directory with Markdown file per lesson

**Purpose**: Preserve knowledge, track improvements, identify patterns.

**Workflow**: Consider defining `learn-from-mistakes` workflow for frequent collaboration (analyze → root cause → design solutions → implement → validate/document).

## Getting Started

### For Project Teams

To adopt CAFE in your project, use this bootstrap prompt with any agent:

```
Read the CAFE framework specification from https://github.com/svens/cafe/blob/v2/cafe.md and create an agent entry point file in the project root following the structure described in the AGENTS.md section of the CAFE specification.

The agent entry point should serve as the orientation guide for agents and include:
- Project overview (goals, technology stack, development workflow)
- Agent Infrastructure Guide (available actions, roles, workflows)
- Agent Guidelines (coding standards, testing, documentation)
- Maintenance Practices (discovery strategies, structural changes, lessons learned)
- Getting Started (orientation for new agents, essential validation steps)
- Common Tasks (typical workflows and agent infrastructure usage)

Also implement the actual `.agents/` infrastructure (actions, roles, workflows) referenced in the agent entry point, tailored to this project's specific technology stack and development practices.
```

### For Existing Projects

Adopt incrementally. Start with agent entry point documenting existing practices. Add actions/roles/workflows only when they add value.

### For Individual Agents

1. Read the agent entry point first (typically `AGENTS.md`)
2. Check `.agents/actions/` before manual solutions
3. Use roles from `.agents/roles/` for specialized work
4. Follow documentation context hierarchy
5. Apply discovery strategies for structural changes
6. Update documentation with code (doc-first)
7. Use root cause analysis for mistakes
