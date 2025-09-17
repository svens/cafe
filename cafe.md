# CAFE: Contextual Agent Framework Environment

## Philosophy
- **Doc-first**: Documentation drives development, not the reverse
- **Human-agent collaboration**: Humans focus on ideas and architecture, agents handle implementation
- **Project freedom**: Minimal constraints, maximum flexibility in project organization
- **Context-aware**: Agents understand project structure through discoverable conventions

## Core Concepts

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
- Stored in repository root: `.agents/roles/ROLE_NAME.md`
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
  - **Role:** Agent role to assume for the step (role must exist in `.agents/roles/`)
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
- Workflows are interruptible/resumable via user interface (claude, opencode, IDEs, etc.)

**Context Passing:**
- Step results automatically available as `{{previous_step_result}}` in next step
- Named outputs: steps can export `{{step_name.output_key}}` for later reference
- Global context: `{{workflow.context}}` persists across all steps

### Documentation Context Resolution
README.md files provide context that cascades from generic (parent) to specific (child) directories.

**README.md Resolution:**
1. Start from repository root README.md (base context)
2. Walk down to current directory, collecting README.md from each level
3. Merge contexts: parent provides general rules, child adds/overrides specifics
4. Agents receive combined context when working in any directory
5. Child README.md can reference parent sections or completely override them

## Project Structure
Projects organize themselves freely. CAFE only requires:
- `.agents/` directories for CAFE-specific files (actions, roles, workflows)
- `README.md` files for contextual documentation
- No prescribed folder layouts or naming conventions

## Workflow
1. **Document**: Write README.md describing goals, APIs, constraints
2. **Implement**: Agents generate code using documentation as context
3. **Validate**: Run actions (test, lint, build) to verify implementation
4. **Iterate**: Refine docs and code together

## Integration
CAFE works with any toolchain by providing discoverable conventions:
- Actions for common operations (build, test, deploy, lint)
- Roles for specialized agent behavior
- Workflows for multi-step agent processes
- Documentation for context and constraints
- Tool-agnostic approach - no vendor lock-in

## Performance Considerations
- Discovery results (actions, roles, workflows, etc) cached in `.agents/cache.yaml` (relative paths from project root)
- Cache invalidated based on `.agents/` directory modification times
- Cache file should be added to `.gitignore` (project-local, not version controlled)

## Agent Guidelines
- Always check for relevant actions before implementing manual solutions
- Respect README.md context hierarchy when making decisions
- Use appropriate role prompts for the task at hand
- Maintain doc-first discipline: update documentation with code changes

## Getting Started

### For Project Teams
To adopt CAFE in your project, use this bootstrap prompt with any agent:

```
Read the CAFE framework specification from https://github.com/svens/cafe/blob/v2/cafe.md and create an AGENTS.md file in the repository root that serves as the general entry point for various agents working on this project.

The AGENTS.md should:

1. **Project Overview**
   - Brief description of the project and its goals
   - Technology stack and key frameworks used
   - Development workflow and team practices

2. **CAFE Infrastructure Guide**
   - Available actions in `.agents/actions/` and when to use them
   - Defined roles in `.agents/roles/` and their purposes
   - Existing workflows in `.agents/workflows/` and their use cases

3. **Agent Guidelines**
   - Project-specific coding standards and conventions
   - Testing requirements and quality gates
   - Documentation maintenance expectations
   - Context resolution hierarchy (README.md cascade)

4. **Getting Started**
   - How new agents should orient themselves to the project
   - Essential actions to run for project setup/validation
   - Key files and directories to understand

5. **Common Tasks**
   - Typical development workflows and which CAFE components to use
   - How to extend the CAFE infrastructure for new needs

Also implement the actual `.agents/` infrastructure (actions, roles, workflows) referenced in the AGENTS.md file, tailored to this project's specific technology stack and development practices.
```

### For Individual Agents
When working on a CAFE-enabled project:
1. Read the project's `AGENTS.md` file first for project-specific guidance
2. Check available actions in `.agents/actions/` before implementing manual solutions
3. Use appropriate roles from `.agents/roles/` for different types of work
4. Follow the README.md context hierarchy when making decisions
5. Maintain doc-first discipline: update documentation with code changes
