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

**Avoiding Duplication:**
- **Document once, reference elsewhere**: When information applies to multiple contexts, establish a canonical location and reference it from other locations
- **Identify cross-cutting information**: If the same guidance appears in 3+ files, it's a candidate for centralization
- **Use cross-references**: Link to canonical documentation rather than duplicating content
- **Context hierarchy determines ownership**: Parent-level concerns belong in parent docs, child-specific details in child docs

### Documentation Principles

CAFE projects follow the DRY (Don't Repeat Yourself) principle for documentation to maintain consistency and reduce maintenance burden.

**When to Document Inline vs. Reference:**

✅ **Document inline** when:
- Information is specific to this file's context only
- Content is brief (1-2 sentences) and tightly coupled to surrounding content
- Duplication would be minimal and unlikely to change independently

✅ **Document once, reference elsewhere** when:
- Information applies to multiple contexts or files
- Content is detailed (procedures, conventions, explanations)
- Changes would require updates in multiple locations
- Information represents a canonical definition or standard

**Identifying Cross-Cutting Information:**

Ask these questions when documenting:
1. **Does this information appear elsewhere?** Search for similar content in other docs
2. **Will this information be referenced from multiple contexts?** Consider future use
3. **Is this a project-wide convention or principle?** Belongs in AGENTS.md or CAFE.md
4. **Would changes here require updates elsewhere?** Strong signal for centralization

**Cross-Referencing Best Practices:**

**Internal references** (same file):
```markdown
See [Section Name](#section-anchor) for details
```

**Cross-file references** (within project):
```markdown
See FILENAME "Section Name" for the canonical guidance
See [FILENAME](path/to/file.md#section-anchor) for details
```

**Examples:**

❌ **Bad - Duplicated across 4 files:**
```markdown
# File 1
Extract username: `git config user.email | cut -d'@' -f1`

# File 2
Extract username: `git config user.email | cut -d'@' -f1`

# File 3
Extract username: `git config user.email | cut -d'@' -f1`
```

✅ **Good - Document once, reference elsewhere:**
```markdown
# AGENTS.md (canonical location)
## Username Extraction
Extract username: `git config user.email | cut -d'@' -f1`

# File 1
See AGENTS.md "Username Extraction" section

# File 2
See AGENTS.md "Username Extraction" section
```

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

## Managing Structural Changes

When project structure changes (adding/removing/renaming major components), multiple files need updates. A systematic approach prevents inconsistencies.

### Sync Markers

**Key principle:** Sync markers are topic discovery aids, not format specifications. They help find all locations discussing a structural concept, regardless of representation format.

Mark sections that reference project structure with comments using idiomatic syntax for each language:

```html
<!-- SYNC:STRUCTURE - Component list -->
```

```bash
# SYNC:STRUCTURE - Component list
```

```rust
// SYNC:STRUCTURE - Component list
```

The pattern `SYNC:TOPIC` remains consistent; only comment syntax adapts to the language.

**Finding markers:**
```sh
# Find all sync markers
grep -r "SYNC:" .

# Find specific topic
grep -r "SYNC:STRUCTURE" .
```

**Naming conventions:**

Use topic-focused names describing the concept being synchronized:
- ✅ Good: `SYNC:STRUCTURE`, `SYNC:DEPENDENCIES`, `SYNC:CAFE:ACTIONS`
- ❌ Avoid: `SYNC:COMPONENT_LIST`, `SYNC:README_DIAGRAM` (implies format/location)

Consider prefixes for organization:
- `SYNC:CAFE:*` - CAFE infrastructure (actions, roles, workflows)
- `SYNC:*` - Project-specific structure

### When to Use Sync Markers

Add markers when:
- Structural element referenced in multiple files
- Changes require coordinated updates across files
- Different file types reflect the same logical structure
- Content is a discrete structural artifact (lists, diagrams, code blocks)

Skip markers for:
- Single-file references
- Auto-generated content
- Implementation details that don't cross boundaries
- Descriptive prose that embeds component names inline

**Prose vs. Artifacts**:

Sync markers work best for **structured artifacts** (bulleted lists, code sections, diagrams) where component names appear in predictable formats. For **descriptive prose** that naturally embeds component names (e.g., "The client and server components both use..."), use direct text search instead:

```sh
# Find all mentions of a component in documentation
rg "component_name" README.md AGENTS.md

# Case-insensitive search for variations
rg -i "server" AGENTS.md
```

When performing structural changes, use both strategies:
1. Search sync markers to find structural artifacts
2. Search component names directly to find prose descriptions

### Workflow Pattern

For frequent structural changes:
1. **Plan**: Search for sync markers to identify affected artifacts
2. **Update**: Modify each location appropriately for its context
3. **Validate**: Run actions to verify changes
4. **Review**: Search markers again to confirm completeness

Use architect role (if defined) for planning and review steps.

**For projects with frequent structural changes**, consider defining this as a workflow in `.agents/workflows/` (see Workflows section).

### Validation

After structural changes:
- Run all actions (build, test, custom validations)
- Search for sync markers to verify all updates made
- Check documentation links still work
- Verify CAFE actions/workflows execute correctly
- Ensure logical consistency across different representations

## Continuous Improvement

CAFE projects benefit from systematic learning and improvement based on agent experiences.

### Learning From Mistakes

When agents make mistakes despite following documentation and validations, these represent opportunities to improve the CAFE infrastructure itself.

**Common mistake categories:**
- **CAFE gaps**: Missing patterns, insufficient discovery, unclear context resolution
- **Project documentation gaps**: Ambiguous guidance, missing examples, hard-to-find information
- **Validation gaps**: Missing automated checks for important invariants
- **Workflow gaps**: Complex tasks without systematic guidance

**Out of scope**: Trivial issues (typos, minor inconsistencies) that don't reveal systematic problems.

### Root Cause Analysis Pattern

Use systematic analysis to identify fundamental causes:

1. **5-Whys Method** (minimum 3, maximum 5 iterations):
   - Start with the mistake: "Why did [X] happen?"
   - Continue asking "why" for each answer until reaching root cause
   - Root cause typically reveals missing documentation, unclear patterns, or validation gaps

2. **Fishbone Analysis** (if multiple distinct causes):
   - **People**: Role clarity, responsibility boundaries
   - **Process**: Workflow availability, action coverage
   - **Documentation**: README.md completeness, CAFE.md clarity
   - **Tools**: Validation scripts, sync markers, infrastructure

3. **Scope Determination**:
   - CAFE framework issue (affects any CAFE project)
   - Project-specific issue (unique to this codebase)
   - Both (CAFE principle applied incorrectly in project context)

### Lessons Learned Repository

**Structure:** `.agents/lessons/<yyyy>_<mm>_<dd>_<username>-<short-subject>.md`

**Content:**
- Issue description and impact
- Complete root cause analysis (5-whys, fishbone if applicable)
- Accepted solution with implementation checklist
- Validation approach
- Prevention measures

**Purpose:**
- Preserve institutional knowledge about common pitfalls
- Track improvements to CAFE principles and project documentation
- Identify patterns requiring broader infrastructure changes
- Enable periodic review for systematic improvements

**Discovery:**
```sh
# Find by category
rg "Category.*CAFE gap" .agents/lessons/

# Find incomplete action items
rg "\[ \]" .agents/lessons/
```

### Improvement Workflow Pattern

For projects with frequent agent collaboration, consider defining a `learn-from-mistakes` workflow (use the architect role if available, unless overwritten by project context):

- **Step 1**: Analyze and classify the mistake (analytical temperature)
- **Step 2**: Root cause analysis using 5-whys/fishbone (analytical temperature)
- **Step 3**: Design solutions (exploratory temperature)
- **Step 4**: Implement improvements to CAFE/project infrastructure (default agent)
- **Step 5**: Validate and document lesson (analytical temperature)

This workflow ensures systematic continuous improvement rather than ad-hoc fixes.

## Performance Considerations
- Discovery results (actions, roles, workflows, etc) cached in `.agents/cache.yaml` (relative paths from project root)
- Cache invalidated based on `.agents/` directory modification times
- Cache file should be added to `.gitignore` (project-local, not version controlled)

## Agent Guidelines
- Always check for relevant actions before implementing manual solutions
- Respect README.md context hierarchy when making decisions
- Use appropriate role prompts for the task at hand
- Maintain doc-first discipline: update documentation with code changes
- Document mistakes systematically to improve CAFE infrastructure

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

4. **Maintenance Practices**
   - How to use sync markers to track structural dependencies
   - When to use the structural change workflow
   - Continuous improvement and lessons learned
   - Cache maintenance and invalidation

5. **Getting Started**
   - How new agents should orient themselves to the project
   - Essential actions to run for project setup/validation
   - Key files and directories to understand

6. **Common Tasks**
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
5. When making structural changes, use sync markers and follow the validation workflow
6. Maintain doc-first discipline: update documentation with code changes
7. When mistakes occur, use systematic root cause analysis to improve infrastructure
