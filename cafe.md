# CAFE: Contextual Agent Framework Environment

[[_TOC_]]

## About This Framework

CAFE provides conventions and patterns for agent-human collaboration, but **every concept is optional**. Projects can:

- Adopt only the pieces that add value (e.g., just AGENTS.md, or just actions)
- Ignore sections that don't fit their needs
- Customize patterns to match existing workflows
- Mix CAFE with other approaches

This is a framework of **suggestions, not requirements**. Use what helps, skip what doesn't.

## Philosophy
- **Doc-first**: Documentation drives development, not the reverse
- **Human-agent collaboration**: Humans focus on ideas and architecture, agents handle implementation
- **Project freedom**: Minimal constraints, maximum flexibility in project organization
- **Context-aware**: Agents understand project structure through discoverable conventions

## Core Concepts

### Naming Conventions

CAFE uses standard names throughout this documentation for clarity and consistency (`AGENTS.md`, `.agents/`, `README.md`, etc.), but **all names are examples only**. Projects are free to choose alternatives that fit their conventions.

**Flexibility Principle:** Every CAFE concept (agent entry point, infrastructure directory, documentation files, subdirectories) can use project-specific names. The agent entry point document must explain the project's chosen naming conventions and how to discover CAFE infrastructure.

**Examples of alternatives:**
- Agent entry point: `AGENTS.md`, `AI.md`, `AGENT_GUIDE.md`, `.agent-docs.md`
- Infrastructure directory: `.agents/`, `.ai/`, `.cafe/`, `agents/`, `ai-tools/`
- Documentation: `README.md` hierarchy, `GUIDE.md`, `docs/`, wiki links
- Subdirectories: `actions/`→`scripts/`, `roles/`→`personas/`, `workflows/`→`processes/`

**Important:** When using non-standard names, document them prominently in your agent entry point so agents can discover and use CAFE infrastructure correctly.

### Agent Entry Point
Projects using CAFE have a primary entry point document in the project root that serves as the orientation guide for agents working on the project.

**Purpose:**
- First file agents read when starting work on a project
- Links together all CAFE infrastructure (actions, roles, workflows)
- Documents project-specific conventions and guidelines
- Explains how to use project's documentation structure

**Key Content:**
- **CAFE Infrastructure Guide**: Available actions, roles, workflows and when to use them
- **Agent Guidelines**: Coding standards, testing requirements, quality gates
- **Maintenance Practices**: Discovery strategies, structural change workflows, lessons learned
- **Getting Started**: Orientation for new agents, essential setup/validation steps
- **Common Tasks**: Typical workflows and which CAFE components to use

**Best Practice:** Every agent invocation should start by reading this entry point document to understand the project's structure, conventions, and available CAFE infrastructure before proceeding with work. This provides both comprehensive orientation and efficient discovery without directory walking.

**Project Boundary:** The location of AGENTS.md defines the project root for CAFE purposes. Repositories can contain multiple projects using CAFE, each with their own AGENTS.md and `.agents/` infrastructure operating independently.

### Actions
Actions are executable tasks that agents can invoke to build, test, deploy, or perform other project operations.

**Action Resolution Algorithm:**
1. Start in current working directory
2. Look for `.agents/actions/ACTION_NAME` (executable file)
3. If not found, walk up directory tree checking each parent's `.agents/actions/`
4. If action still missing, report missing dependency
5. Execute the found action from the directory where it was discovered, not changing current working directory (unless action itself changes it)

Actions can be any executable format (shell scripts, Python, etc.) - no mandatory schema required.

**Action Execution:**
- Actions execute from discovery directory, preserving current working directory
- stdout/stderr captured and available to calling agent
- Exit codes determine success/failure for workflow control

### Roles
Roles define how agents should interact within specific contexts through system prompts.

**Role Structure:**
- Stored in project root: `.agents/roles/ROLE_NAME.md`
- Roles are centralized because they define project-wide agent behavior and context
- Each role is a markdown file containing the agent prompt
- Optional YAML frontmatter for role configuration (temperature, model preferences, etc.)
- Roles can extend base roles using `extends: base_role_name` in frontmatter
- Inheritance allows role composition and hierarchies (e.g., `frontend-developer` extends `developer`)
- Example roles: `architect`, `reviewer`, `tester`, `documenter`

### Workflows
Workflows define agent-oriented series of steps to achieve complex goals like generating code from updated docs, reviewing changesets, or performing maintenance.

**Workflow Structure:**
- Stored in `.agents/workflows/WORKFLOW_NAME.md`
- Each workflow is a markdown file with steps containing:
  - **Goal:** Short step objective
  - **Role:** Agent role to assume for the step (see [Roles](#roles) section)
  - **Description:** Detailed step explanation
  - **Agent Prompt:** Prompt to achieve the goal

**Error Handling:**
- Steps can specify `on_failure: continue|stop|retry` behavior
- Failed steps pass error context to subsequent steps
- Workflows can define rollback actions

**Workflow Resolution Algorithm:**
1. Look for `.agents/workflows/WORKFLOW_NAME.md` in current directory
2. If not found, walk up directory tree checking each parent's `.agents/workflows/`
3. Execute from directory where workflow was discovered, not changing current working directory (unless workflow itself changes it)

**Execution:**
- Humans invoke workflows through agent interface (Claude, IDEs, etc.)
- Each step can specify different roles for context switching
- Workflows are interruptible/resumable via user interface (Claude, OpenCode, IDEs, etc.)

**Context Passing:**
Workflows executing within a single agent session can pass context between steps. The specific mechanism depends on the agent platform implementation, but typically includes:
- Access to previous step results for sequential processing
- Named outputs that steps can export for later reference by subsequent steps
- Global workflow context that persists across all steps in the session
- Error context from failed steps passed to subsequent steps (see Error Handling above)

### Documentation Context Resolution
README.md files provide context that cascades from generic (parent) to specific (child) directories.

**README.md Resolution:**
1. Start from project root README.md (base context)
2. Walk down to current directory, collecting README.md from each level
3. Merge contexts: parent provides general rules, child adds/overrides specifics
4. Agents receive combined context when working in any directory
5. Child README.md can reference parent sections or completely override them

**Avoiding Duplication:**
See the [Documentation Principles](#documentation-principles) section below for guidance on the DRY principle and when to centralize vs. reference documentation.

### Documentation Principles

Projects using CAFE follow the DRY (Don't Repeat Yourself) principle for documentation to maintain consistency and reduce maintenance burden.

**Document once, reference elsewhere:**
- Information applying to multiple contexts belongs in a canonical location
- Use cross-references to avoid duplication
- Context hierarchy determines ownership: parent-level concerns in parent docs, child-specific details in child docs

**Identifying cross-cutting information:**
- Does this appear elsewhere? Search before documenting
- Will this be referenced from multiple contexts?
- Would changes require updates in multiple locations?

## Project Structure
Projects organize themselves freely. CAFE conventions include:
- **Agent entry point** in project root (typically `AGENTS.md`) - orientation guide for agents
- `.agents/` directories for CAFE infrastructure (actions, roles, workflows)
- `README.md` files for contextual documentation
- No other prescribed folder layouts or naming conventions

**Multiple Projects in One Repository:**
Monorepos can contain multiple independent projects using CAFE, each marked by its own AGENTS.md:
- Each AGENTS.md defines a separate project root
- Directory-walking for actions/roles/workflows stops at project root
- Projects can share code but maintain separate CAFE infrastructure
- Useful for microservices, multi-component systems, or organizational code sharing

## Workflow
1. **Document**: Write documentation describing goals, APIs, constraints
2. **Plan**: Break down work into tasks, identify dependencies, choose approaches
3. **Implement**: Agents generate code using documentation as context
4. **Validate**: Run actions (test, lint, build) to verify implementation
5. **Iterate**: Refine docs and code together

## TODO Management

CAFE does not prescribe TODO tracking - projects can use any approach or none at all. This section provides guidance for projects that choose to implement systematic TODO management for long-running or complex agent work.

Long-running or complex work benefits from explicit task tracking. Projects may implement TODO management systems using CAFE infrastructure or integrate with external tools.

### When TODO Management Adds Value

- **Complex multi-step tasks**: Work requiring coordination across multiple sessions
- **Long-term projects**: Features spanning days/weeks with multiple agents
- **Context preservation**: Maintaining state across interruptions or agent switches
- **Progress visibility**: Human oversight of agent work progress

### Design Considerations

Projects implementing TODO management should consider:

**Storage Options**:
- File-based in `.agents/todos/` (CAFE infrastructure approach)
- File-based in `todos/` at project root (project-level approach)
- Project root `TODO.md` or `TODOS.md` (simple projects)
- External systems (GitHub Issues, Jira, Linear, etc.)
- Human-readable format for git workflows and manual editing
- Discoverable through CAFE's directory-walking pattern (if file-based)

**Organization**:
- Hierarchical task breakdown (projects → tasks → subtasks)
- Status tracking (pending, in-progress, blocked, completed)
- Priority classification appropriate to project needs
- Context linking (RFCs, lessons learned, architecture docs)

**Integration Approaches**:
- **File-based**: Actions for operations (add, list, update, archive), workflows for patterns (planning, tracking, completion)
- **External systems**: CLI integration actions (e.g., `gh issue`, `jira-cli`), API wrappers for agent use
- **Hybrid**: File-based for agent working state, external system for team coordination

**Scope Determination**:
- Define granularity appropriate to project scale (feature-level vs file-level)
- Consider team size and collaboration needs (single-user vs multi-user)
- Establish archival and cleanup policies
- Balance between structure and overhead

### Context Passing

TODO systems preserve context across sessions through persistent references:
- Link TODOs to relevant documentation (RFCs, architecture docs, lessons learned)
- Reference workflow results or decisions that created the TODO
- Include file/line references for implementation tasks
- Link to external tracking systems for team coordination
- Workflows can query TODO state for decision-making across sessions

## Integration
CAFE works with any toolchain by providing discoverable conventions:
- Actions for common operations (build, test, deploy, lint)
- Roles for specialized agent behavior
- Workflows for multi-step agent processes
- Documentation for context and constraints
- Tool-agnostic approach - no vendor lock-in

## Managing Structural Changes

When project structure changes (adding/removing/renaming major components), multiple files may need coordinated updates. A systematic approach prevents inconsistencies.

**Examples of structural changes:**
- Adding/removing components, modules, or services
- Renaming core abstractions or interfaces
- Restructuring directory hierarchies
- Adding/removing CAFE infrastructure (actions, roles, workflows)

**Why this needs special handling:** Regular validation (build/test) may not catch all inconsistencies. Documentation, examples, and configuration files can become stale even when code compiles successfully.

### Discovery Strategies

Projects can choose approaches to identify locations requiring updates:
- **Search-based**: Text/pattern search for component references (e.g., `rg "old_component_name"`)
- **Validation-enforced**: Automated checks (build/test failures, linting, CI)
- **Sync markers**: Explicit topic markers in comments (e.g., `SYNC:STRUCTURE`)

**Choose based on**: Project size, component coupling, team needs, available tooling.

### Workflow Pattern

Use this systematic approach:
1. **Plan**: Identify all affected locations using your chosen discovery strategy
2. **Update**: Modify each location appropriately for its context
3. **Validate**: Run actions to verify changes (build, test, lint)
4. **Review**: Confirm completeness by applying discovery strategy again

For projects with frequent structural changes, consider defining this as a workflow in `.agents/workflows/` (see [Workflows](#workflows) section).

## Continuous Improvement

Projects using CAFE benefit from systematic learning and improvement based on agent experiences.

### Learning From Mistakes

When agents make mistakes despite following documentation and validations, these represent opportunities to improve the CAFE infrastructure.

**Common mistake categories:**
- **CAFE gaps**: Missing patterns, insufficient discovery, unclear context resolution
- **Project gaps**: Ambiguous guidance, missing validation, workflow gaps

**Out of scope**: Trivial issues (typos, minor inconsistencies) that don't reveal systematic problems.

### Root Cause Analysis

Use systematic analysis to identify fundamental causes:
- **5-Whys Method**: Start with the mistake, ask "why" until reaching root cause (typically 3-5 iterations)
- **Fishbone Analysis**: For multiple distinct causes, examine People, Process, Documentation, and Tools
- **Scope Determination**: CAFE framework issue, project-specific issue, or both

### Lessons Learned Repository

**Storage**: `.agents/lessons/<yyyy>_<mm>_<dd>_<username>-<short-subject>.md`

**Purpose**: Preserve institutional knowledge, track improvements, identify patterns, enable periodic review.

### Improvement Workflow Pattern

For projects with frequent agent collaboration, consider defining a `learn-from-mistakes` workflow with systematic steps: analyze, root cause analysis, design solutions, implement, validate and document (see [Workflows](#workflows) section for structure).

## Getting Started

### For Project Teams

To adopt CAFE in your project, use this bootstrap prompt with any agent:

```
Read the CAFE framework specification from https://github.com/svens/cafe/blob/v2/cafe.md and create an agent entry point file in the project root following the structure described in the "Agent Entry Point" section of the CAFE specification.

The agent entry point should serve as the orientation guide for agents and include:
- Project overview (goals, technology stack, development workflow)
- CAFE Infrastructure Guide (available actions, roles, workflows)
- Agent Guidelines (coding standards, testing, documentation)
- Maintenance Practices (discovery strategies, structural changes, lessons learned)
- Getting Started (orientation for new agents, essential validation steps)
- Common Tasks (typical workflows and CAFE component usage)

Also implement the actual `.agents/` infrastructure (actions, roles, workflows) referenced in the AGENTS.md file, tailored to this project's specific technology stack and development practices.
```

### For Existing Projects

CAFE can be adopted incrementally - use only what adds value:

**Minimal Start:**
- Create agent entry point documenting existing practices and conventions
- Reference existing documentation structure (documentation hierarchy, wikis, or other docs)
- No infrastructure changes needed initially

**Expanding Incrementally:**
- Add actions only for operations agents repeatedly perform manually
- Keep using existing CI, build scripts, and team tooling
- Add roles when specialized agent contexts provide value
- Create workflows for recurring multi-step agent processes
- Establish lessons learned when patterns emerge

**Key Principle:** CAFE complements existing workflows rather than replacing them. Focus on agent-specific conveniences, not general team tooling.

### For Individual Agents
When working on a project using CAFE:
1. **Read the agent entry point first** (typically `AGENTS.md`) - this orients you to the project's structure, conventions, and available CAFE infrastructure
2. Check available actions in `.agents/actions/` before implementing manual solutions
3. Use appropriate roles from `.agents/roles/` for different types of work
4. Follow the documentation context hierarchy when making decisions
5. When making structural changes, use discovery strategies and validation workflows
6. Maintain doc-first discipline: update documentation with code changes
7. When mistakes occur, use systematic root cause analysis to improve infrastructure
