# CAFE Demo: Hello World C++ Project

This demo showcases the CAFE (Contextual Agent Framework Environment, see cafe.md) approach using a simple C++ "Hello World" project.

## Quick Start

For a streamlined experience, you can bootstrap this project using the template from the **Getting Started** section in [cafe.md](cafe.md):

1. Use the bootstrap prompt to create `AGENTS.md` and `.agents/` infrastructure
2. Follow the generated `AGENTS.md` for project-specific guidance
3. Use CAFE actions, roles, and workflows for development

## Manual Demo (Step-by-Step Learning)

**IMPORTANT:** This detailed demo should be executed step-by-step by participants to experience the incremental nature of CAFE development:

1. **Execute one task at a time** - Feed each agent prompt to your agent assistant and complete the task fully
2. **Examine the results** - Review what the agent created and understand the CAFE concepts demonstrated
3. **Commit your changes** - Use git to commit the completed work before proceeding to the next step
4. **Update demo.md after each task** - Change `[ ]` to `[x]` for the completed task
5. **Build understanding incrementally** - Each step adds a new CAFE concept

This hands-on approach demonstrates how CAFE supports iterative, documentation-driven development where participants experience how each step builds upon the previous foundation.

## Demo Task List

### Phase 1: Setup & Documentation

#### [x] 1. Design demo flow structure and sequence
**Goal:** Plan the complete demonstration structure and workflow
**Description:** Define project layout, demo phases, and key concepts to showcase
**Agent Prompt:**
```
Design a demo flow for CAFE framework using a C++ hello world project. Show how actions, roles, and cascading documentation work together. Structure should demonstrate context resolution and agent role switching.
```

#### [ ] 2. Create project structure with CMake setup
**Goal:** Establish basic project directories and CMake configuration
**Description:** Create hello-world project with lib/ and app/ subdirectories, basic CMakeLists.txt files
**Agent Prompt:**
```
Create a C++ project structure with:
- Root CMakeLists.txt for overall project
- lib/ subdirectory for greeting library
- app/ subdirectory for main application
- Basic CMake configuration linking app to lib
Follow modern CMake practices.
```

### Phase 2: CAFE Infrastructure

#### [ ] 3. Create CAFE roles (developer, reviewer)
**Goal:** Define AI behavioral prompts for different work contexts
**Description:** Create role files that specify how agents should behave when implementing vs reviewing code
**Agent Prompt:**
```
Create two CAFE roles:
1. developer.md - Focus on implementation, following coding standards, meeting requirements
2. reviewer.md - Focus on code quality, maintainability, security considerations, best practices
Each role should be a clear prompt defining agent behavior and priorities.
```

#### [ ] 4. Create CAFE actions (build, test, clean)
**Goal:** Provide discoverable executable tasks for common operations
**Description:** Create shell scripts for build, test, and clean operations using CMake
**Agent Prompt:**
```
Create executable CAFE actions:
- build: Configure and build the project using CMake
- test: Run any tests (even if just running the executable)
- clean: Clean build artifacts
Make scripts robust and informative with proper error handling.
```

### Phase 3: Documentation Context

#### [ ] 5. Create cascading README.md documentation
**Goal:** Demonstrate how context flows from parent to child directories
**Description:** Write README.md files at root, lib/, and app/ levels showing context inheritance
**Note:** The root README.md can be completely rewritten during this step to contain project-specific content instead of just pointing to demo.md
**Agent Prompt:**
```
Create cascading README.md files:
- Root: Project overview, build requirements, C++ standards (replace the current README.md with full project documentation)
- lib/: Library API design, function specifications, usage guidelines
- app/: Application purpose, command-line interface, examples
Show how child contexts build upon parent contexts.
```

### Phase 4: Implementation & Review

#### [ ] 6. Create workflow for code implementation and review
**Goal:** Create a reusable workflow demonstrating multi-step AI process
**Description:** Define workflow that implements code, builds, and reviews using different roles
**Agent Prompt:**
```
Create a CAFE workflow `.agents/workflows/implement-and-review.md` that:
1. Uses developer role to implement code per README requirements
2. Uses build action to compile and verify
3. Switches to reviewer role to assess code quality
4. Documents findings and recommendations
Include proper step configuration with roles and dependencies.
```

#### [ ] 7. Execute the implementation workflow
**Goal:** Demonstrate workflow execution and role switching
**Description:** Run the workflow to implement the greeting library and main app
**Agent Prompt:**
```
Execute the implement-and-review workflow to:
1. Implement the greeting library and main application per README requirements
2. Build and verify the implementation
3. Review code quality, security, and maintainability
4. Show how actions work from any directory (action resolution algorithm)
Document the complete process and findings.
```

## Project Requirements

The hello-world project should implement:

### Library Functions (lib/)
- `std::string greeting()` - returns "hello"
- `std::string who()` - returns "world"  
- `void welcome(std::ostream &stream, const std::string &greeting, const std::string &who)` - outputs "greeting, who"

### Application (app/)
- Uses library functions to output "hello, world"
- Demonstrates proper library integration

### Build System
- Modern CMake (3.15+)
- Proper target linking
- Clean separation between library and application

## Key Demo Points

1. **Context Cascading:** How README.md files provide layered context
2. **Action Discovery:** How agents find and use actions from any directory
3. **Role Switching:** How different agent roles approach the same codebase
4. **Workflow Execution:** How multi-step agent processes are defined and executed
5. **Doc-First Approach:** How documentation drives implementation decisions
6. **Project Freedom:** How CAFE works with any project structure

## Expected Outcomes

- Participants understand CAFE's minimal approach
- Clear demonstration of human-agent collaboration workflow
- Evidence that documentation-driven development works effectively
- Showcase of flexible, discoverable automation
