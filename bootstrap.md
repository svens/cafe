# CAFE Architecture Bootstrap Guide

This guide helps you start a new project based on the CAFE (AI/doc-first monorepo) architecture principles.

## Core Philosophy

The CAFE architecture optimizes for AI-human collaboration by:
- **Doc-first development**: All features start with documentation
- **Human steering**: Humans focus on architecture, ideas, and documentation  
- **AI execution**: AI handles implementation, testing, pipelines, and deployment
- **Action-based workflow**: Executable documentation drives automation

## Quick Start Checklist

### 1. Initialize Repository Structure

```bash
# Core directories
mkdir -p {base,silo,patterns,docs/{knowledge,postmortems},pipelines/templates}
mkdir -p .cafe/{actions,templates}

# Base framework (replace 'base' with your domain)
mkdir -p base/.cafe/actions

# Sample silo structure  
mkdir -p silo/{playground,sample}/.cafe/actions
```

### 2. Create Root Documentation

Create these essential files in repository root:

#### `README.md` (Architecture overview)
```markdown
# [Project Name] - CAFE Architecture

## Philosophy
- Doc-first development: Features start with documentation
- AI handles implementation, humans steer architecture
- Action-based workflow with executable documentation

## Structure
- `/base/` - Shared framework and actions
- `/silo/*/` - Independent services inheriting from base
- `/patterns/` - Reusable implementation patterns
- `/docs/knowledge/` - FAQs and learnings

## Development Workflow
1. Write feature documentation first
2. AI generates implementation from docs
3. Review and refine AI output
4. Actions handle build/test/deploy
```

#### `CLAUDE.md` (AI toolchain integration)
```markdown
# AI Development Guidelines

Read `README.md` for complete architecture overview.

## Action Discovery
Follow hierarchical action resolution:
1. Local `.cafe/actions/` in current directory
2. Traverse up directory tree
3. Fall back to base framework actions

## Development Rules
- Always start with documentation
- Use existing patterns before creating new ones
- Maintain action schema consistency
- Test actions independently and in composition
```

### 3. Define Action Schema

Create `.cafe/templates/action-template.md`:

```yaml
---
description: Brief purpose of this action
inputs: []           # Array of input parameters (optional)
outputs: string      # Description of expected output format
dependencies: []     # Other actions this depends on (optional)  
ai_notes: Instructions for AI when executing this action
command: |
  # Shell commands to execute
  echo "action output"
---

# Action Name

Detailed description of what this action does and how to use it.

## Usage Context
When and how this action should be used.

## Examples  
Concrete usage examples.
```

### 4. Create Base Framework Actions

Essential base actions in `base/.cafe/actions/`:

#### `build.md`
```yaml
---
description: Build the project using standard build tools
inputs: []
outputs: "Build success/failure status"
ai_notes: Check for package.json, Makefile, or other build configs
command: |
  if [[ -f package.json ]]; then npm run build
  elif [[ -f Makefile ]]; then make
  else echo "No build configuration found"; exit 1
  fi
---
```

#### `test.md`  
```yaml
---
description: Run all project tests
inputs: []
outputs: "Test results and coverage"
ai_notes: Discover and run appropriate test framework
command: |
  if [[ -f package.json ]]; then npm test
  elif [[ -f pytest.ini ]]; then pytest
  else echo "No test configuration found"; exit 1
  fi
---
```

#### `lint.md`
```yaml
---
description: Run code quality checks and linting
inputs: []
outputs: "Lint warnings and errors"  
ai_notes: Use project-specific linting configuration
command: |
  if [[ -f package.json ]] && npm list eslint &>/dev/null; then npm run lint
  elif command -v ruff &>/dev/null; then ruff check .
  else echo "No linting configuration found"
  fi
---
```

### 5. Create Essential Patterns

#### `patterns/validate.md`
```yaml
---
description: Reusable input validation pattern
inputs: ["value", "error_message"]
outputs: "exits with error if validation fails"
ai_notes: Include this pattern in actions that need input validation
command: |
  VALUE="$1"
  ERROR_MSG="${2:-Error: validation failed}"
  if [[ -z "$VALUE" ]]; then
    echo "$ERROR_MSG" >&2
    exit 1
  fi
---
```

#### `patterns/logging.md`
```yaml
---
description: Standardized logging pattern for actions
inputs: ["log_level", "message"]
outputs: "formatted log entry"
ai_notes: Use this for consistent logging across all actions
command: |
  LEVEL="${1:-INFO}"
  MESSAGE="$2"
  TIMESTAMP=$(date '+%Y-%m-%d %H:%M:%S')
  echo "[$TIMESTAMP] [$LEVEL] $MESSAGE"
---
```

### 6. Setup Sample Silo

Create `silo/playground/README.md`:

```markdown
# Playground Silo

Experimental space for testing new ideas and patterns.

## Purpose
- Test new actions safely
- Experiment with patterns
- Prototype features before moving to production silos

## Guidelines  
- AI can scaffold experimental code here
- No production constraints apply
- Use for refactoring useful pieces into patterns
```

Create sample action override in `silo/playground/.cafe/actions/hello.md`:

```yaml
---
description: Sample action for testing silo inheritance
inputs: []
outputs: "greeting message"
ai_notes: This demonstrates silo-specific action creation
command: echo "Hello from playground silo!"
---
```

### 7. Knowledge Base Initialization

#### `docs/knowledge/getting-started.md`
```markdown
# Getting Started with CAFE

## For Developers
1. Read root README.md for architecture overview
2. Explore existing silos to understand patterns
3. Start new features with documentation in `silo/*/docs/features/`

## For AI
1. Always read README.md hierarchically (root → silo → current)
2. Follow action discovery algorithm for proper inheritance
3. Use existing patterns before creating new ones
4. Validate action dependencies before execution
```

#### `docs/knowledge/troubleshooting.md`
```markdown
# Common Issues and Solutions

## Action Not Found
- Check action discovery path: local → parent → base
- Verify YAML frontmatter syntax
- Ensure `.cafe/actions/` directory structure

## Build/Test Failures
- Verify action dependencies exist
- Check working directory context
- Review action command syntax

## Silo Inheritance Issues
- Confirm base framework accessibility
- Remember: local actions completely override base actions
- Test both standalone and composite actions
```

## Development Workflow

### Starting a New Feature

1. **Document First**: Create feature documentation
   ```bash
   mkdir -p silo/[service]/docs/features/
   # Write feature-name.md with requirements and API
   ```

2. **AI Implementation**: Let AI read docs and generate:
   - Implementation code
   - Tests
   - Actions
   - Pipeline updates

3. **Review and Refine**: Human review of AI output
   - Architecture alignment
   - Code quality
   - Documentation completeness

4. **Validate**: Run actions to ensure everything works
   ```bash
   # Test individual actions
   bash .cafe/actions/build.md
   bash .cafe/actions/test.md
   bash .cafe/actions/lint.md
   ```

### Creating a New Silo

1. **Setup Structure**:
   ```bash
   mkdir -p silo/[service-name]/.cafe/actions
   ```

2. **Create Silo README**: Define service-specific rules and constraints

3. **Override Base Actions**: Customize actions for service needs

4. **Test Inheritance**: Verify action discovery works correctly

## AI Prompt Examples

### Architecture Setup
- *"Setup a new CAFE monorepo for a microservices project with authentication and payment silos"*
- *"Create base actions for Node.js project with TypeScript, Jest, and ESLint"*  
- *"Add CI/CD pipeline templates using GitHub Actions"*

### Development
- *"Create a new user-management silo that inherits from base authentication patterns"*
- *"Add input validation pattern to all API actions in payment silo"*
- *"Generate integration tests for cross-silo communication"*

### Maintenance  
- *"Audit all actions for security best practices"*
- *"Update base testing action to support multiple test frameworks"*
- *"Create action dependency visualization"*

## Best Practices

### Documentation
- Start every feature with a doc in `silo/*/docs/features/`
- Keep README.md files updated as silos evolve
- Document action dependencies clearly
- Use consistent YAML schema across all actions

### Action Design
- Make actions atomic and composable
- Include comprehensive `ai_notes` for AI context
- Test actions both standalone and in workflows
- Use patterns for common functionality

### Silo Management
- Keep silos focused and autonomous
- Override base actions only when necessary
- Document silo-specific constraints clearly  
- Test cross-silo integrations regularly

### AI Collaboration
- Provide clear, specific prompts
- Review AI output for architecture alignment
- Use playground silo for experimentation
- Maintain knowledge base with learnings

## Next Steps

1. **Customize Base Framework**: Adapt base actions to your technology stack
2. **Create Domain Silos**: Build service-specific silos with proper inheritance
3. **Establish Patterns**: Develop reusable patterns for common needs
4. **Setup CI/CD**: Create pipeline templates for automated workflows
5. **Build Knowledge Base**: Document learnings and best practices
6. **Team Onboarding**: Train team on CAFE workflow and AI collaboration

## Resources

- **Root cafe.md**: Complete philosophy and architecture details
- **Implementation Example**: See existing shape/silo structure
- **Action Templates**: Use `.cafe/templates/` for consistency
- **Knowledge Base**: Check `docs/knowledge/` for answers to common questions

Remember: CAFE optimizes for human creativity and AI execution. Focus on architecture and documentation, let AI handle the implementation details.