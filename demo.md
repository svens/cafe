# CAFE Demo: Hello World C++ Project

This demo showcases the CAFE (Contextual AI Framework Environment, see cafe.md) approach using a simple C++ "Hello World" project.

## Demo Execution Instructions

**IMPORTANT:** This demo should be executed step-by-step by participants to experience the incremental nature of CAFE development:

1. **Execute one task at a time** - Feed each AI prompt to your AI assistant and complete the task fully
2. **Examine the results** - Review what the AI created and understand the CAFE concepts demonstrated
3. **Commit your changes** - Use git to commit the completed work before proceeding to the next step
4. **Update demo.md after each task** - Change `[ ]` to `[x]` for the completed task
5. **Build understanding incrementally** - Each step adds a new CAFE concept

This hands-on approach demonstrates how CAFE supports iterative, documentation-driven development where participants experience how each step builds upon the previous foundation.

## Demo Task List

### Phase 1: Setup & Documentation

#### [x] 1. Design demo flow structure and sequence
**Goal:** Plan the complete demonstration structure and workflow
**Description:** Define project layout, demo phases, and key concepts to showcase
**AI Prompt:** 
```
Design a demo flow for CAFE framework using a C++ hello world project. Show how actions, roles, and cascading documentation work together. Structure should demonstrate context resolution and AI role switching.
```

#### [ ] 2. Create project structure with CMake setup
**Goal:** Establish basic project directories and CMake configuration
**Description:** Create hello-world project with lib/ and app/ subdirectories, basic CMakeLists.txt files
**AI Prompt:**
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
**Description:** Create role files that specify how AI should behave when implementing vs reviewing code
**AI Prompt:**
```
Create two CAFE roles:
1. developer.md - Focus on implementation, following coding standards, meeting requirements
2. reviewer.md - Focus on code quality, maintainability, security considerations, best practices
Each role should be a clear prompt defining AI behavior and priorities.
```

#### [ ] 4. Create CAFE actions (build, test, clean)
**Goal:** Provide discoverable executable tasks for common operations
**Description:** Create shell scripts for build, test, and clean operations using CMake
**AI Prompt:**
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
**AI Prompt:**
```
Create cascading README.md files:
- Root: Project overview, build requirements, C++ standards
- lib/: Library API design, function specifications, usage guidelines
- app/: Application purpose, command-line interface, examples
Show how child contexts build upon parent contexts.
```

### Phase 4: Implementation & Review

#### [ ] 6. Demonstrate AI workflow using roles and actions
**Goal:** Show complete workflow of implementation and review using CAFE concepts
**Description:** Use developer role to implement, then reviewer role to assess, showing action discovery
**AI Prompt:**
```
Demonstrate complete CAFE workflow:
1. Adopt developer role and implement the greeting library and main app per README requirements
2. Use build action to compile and verify
3. Switch to reviewer role and assess code quality, security, maintainability
4. Show how actions work from any directory (action resolution algorithm)
Document the process and findings.
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
2. **Action Discovery:** How AI finds and uses actions from any directory
3. **Role Switching:** How different AI roles approach the same codebase
4. **Doc-First Approach:** How documentation drives implementation decisions
5. **Project Freedom:** How CAFE works with any project structure

## Expected Outcomes

- Participants understand CAFE's minimal approach
- Clear demonstration of human-AI collaboration workflow
- Evidence that documentation-driven development works effectively
- Showcase of flexible, discoverable automation
