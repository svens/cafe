# CAFE: Contextual AI Framework Environment

## Philosophy
- **Doc-first**: Documentation drives development, not the reverse
- **Human-AI collaboration**: Humans focus on ideas and architecture, AI handles implementation
- **Project freedom**: Minimal constraints, maximum flexibility in project organization
- **Context-aware**: AI understands project structure through discoverable conventions

## Core Concepts

### Actions
Actions are executable tasks that AI can invoke to build, test, deploy, or perform other project operations.

**Action Resolution Algorithm:**
1. Start in current working directory
2. Look for `.cafe/actions/ACTION_NAME` (executable file)
3. If not found, walk up directory tree checking each parent's `.cafe/actions/`
4. If action still missing, report missing dependency
5. Execute the found action from the directory where it was discovered

Actions can be any executable format (shell scripts, Python, etc.) - no mandatory schema required.

### Roles
Roles define how AI should interact within specific contexts through system prompts.

**Role Structure:**
- Stored in repository root: `.cafe/roles/ROLE_NAME.md`
- Each role is a markdown file containing the AI prompt
- Optional YAML frontmatter for role configuration (temperature, model preferences, etc.)
- Example roles: `architect`, `reviewer`, `tester`, `documenter`

### Workflows
Workflows define AI-oriented series of steps to achieve complex goals like generating code from updated docs, reviewing changesets, or performing maintenance.

**Workflow Structure:**
- Stored in `.cafe/workflows/WORKFLOW_NAME.md`
- Each workflow is a markdown file with steps containing:
  - Optional YAML frontmatter for step configuration (role, dependencies)
  - **Goal:** Short step objective
  - **Description:** Detailed step explanation
  - **AI Prompt:** Prompt to achieve the goal

**Workflow Resolution Algorithm:**
1. Look for `.cafe/workflows/WORKFLOW_NAME.md` in current directory
2. If not found, walk up directory tree checking each parent's `.cafe/workflows/`
3. Execute from directory where workflow was discovered

**Step Configuration:**
```yaml
steps:
  - role: developer
    depends_on: [step_id]
```

**Execution:**
- Humans invoke workflows through AI interface (Claude, IDEs, etc.)
- Each step can specify different roles for context switching
- Step results pass context through AI prompts
- Workflows are interruptible/resumable via AI interface

### Documentation Context Resolution
README.md files provide context that cascades from generic (parent) to specific (child) directories.

**README.md Resolution:**
1. Start from repository root README.md (base context)
2. Walk down to current directory, collecting README.md from each level
3. Merge contexts: parent provides general rules, child adds/overrides specifics
4. AI receives combined context when working in any directory
5. Child README.md can reference parent sections or completely override them

## Project Structure
Projects organize themselves freely. CAFE only requires:
- `.cafe/` directories for CAFE-specific files (actions, roles, workflows)
- `README.md` files for contextual documentation
- No prescribed folder layouts or naming conventions

## Workflow
1. **Document**: Write README.md describing goals, APIs, constraints
2. **Implement**: AI generates code using documentation as context
3. **Validate**: Run actions (test, lint, build) to verify implementation
4. **Iterate**: Refine docs and code together

## Integration
CAFE works with any toolchain by providing discoverable conventions:
- Actions for common operations (build, test, deploy, lint)
- Roles for specialized AI behavior
- Workflows for multi-step AI processes
- Documentation for context and constraints
- Tool-agnostic approach - no vendor lock-in

## AI Guidelines
- Always check for relevant actions before implementing manual solutions
- Respect README.md context hierarchy when making decisions
- Use appropriate role prompts for the task at hand
- Maintain doc-first discipline: update documentation with code changes
