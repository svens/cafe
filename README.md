# CAFE Implementation Playground

This project demonstrates the CAFE (AI/doc-first monorepo) architecture using a simple OOP shape example.

## Architecture Overview

See `cafe.md` for complete CAFE philosophy and patterns. This implementation shows:

- **Base framework** (`shape/`) - shared actions and behaviors
- **Silos** - specialized implementations that inherit and override base actions
- **Action-based workflow** - executable documentation in `.cafe/actions/` subdirectories

## Project Structure

```
.cafe/templates/                 # Shared templates
└── test-shape.md               # Testing template for shape silos

docs/knowledge/                  # Knowledge base
└── action-debugging.md         # FAQ for common action issues

patterns/                        # Reusable implementation patterns
└── validate.md                 # Input validation pattern

shape/                           # Base framework
├── .cafe/actions/
│   ├── draw.md                  # Composite action (calls color.md + shape.md)
│   ├── color.md                 # Base color action (echoes "colorless")
│   └── shape.md                 # Base shape action (echoes "shape")

silo/
└── {silo-name}/                 # Generic silo structure (mirrors base)
    ├── README.md                # Silo-specific instructions
    └── .cafe/actions/
        └── {action}.md          # Override or extend base actions
```

## Directory-Specific Documentation

AI should read README.md files hierarchically from repository root to current working directory, applying them in order (root → child → grandchild) so that more specific instructions override generic ones:

1. Start from current working directory
2. Traverse up to repository root, collecting all README.md files
3. Apply instructions in reverse order: root README.md first, then each child level, ending with current directory
4. Later instructions extend or override earlier ones

## Action Schema

Each action is a Markdown file with YAML frontmatter:

```yaml
description: string   # Purpose and behavior
inputs: []           # Input parameters (if any)
outputs: string      # Expected output format
dependencies: []     # Other actions this action depends on (optional)
ai_notes: string     # Instructions for AI execution
command: string      # Shell command to execute
```

## Development Commands

### Base Actions
- `draw` - Draw a shape with color (calls color + shape actions)
- `color` - Get shape color (base: "colorless")  
- `shape` - Get shape type (base: "shape")

## Action Resolution Algorithm

When executing an action, AI should follow this precise discovery order:

1. **Local Actions**: Check current directory `.cafe/actions/{action}.md`
2. **Parent Traversal**: Walk up directory tree checking each `../.cafe/actions/{action}.md`
3. **Base Framework**: Check base framework at `shape/.cafe/actions/{action}.md`
4. **Error**: If action not found in any location, report missing dependency

Example for `draw` action in `silo/circle/`:
```bash
# 1. Check: silo/circle/.cafe/actions/draw.md (not found)
# 2. Check: silo/.cafe/actions/draw.md (not found) 
# 3. Check: .cafe/actions/draw.md (not found)
# 4. Check: shape/.cafe/actions/draw.md (found - use this)
```

## AI Development Guidelines

1. **Action Discovery**: Follow the Action Resolution Algorithm above
2. **Action Execution**: Read action Markdown files and execute their `command` field
3. **Dependency Validation**: Check all dependencies exist before execution
4. **Inheritance Pattern**: Silos inherit all base actions unless explicitly overridden
5. **Context Scope**: When working in a silo, include both silo and base framework in context
6. **Documentation**: Apply hierarchical README.md pattern for complete context
7. **Patterns**: Use `/patterns/` for reusable logic before reinventing
8. **Knowledge**: Check `/docs/knowledge/` for common issues and solutions
9. **Templates**: Extend `/cafe/templates/` for standardized implementations
10. **Experimentation**: Use `/silo/playground/` for testing new ideas safely
11. **Testing**: Verify actions work both independently and in composition

## Quick Start AI Prompts

New users can test the CAFE architecture with these example prompts:

### Basic Testing
- `"Execute draw action from circle silo"`
- `"Show me what actions are available in rectangle silo"`
- `"Run the draw action from base shape framework"`

### Architecture Exploration
- `"Create a new triangle silo that inherits from shape"`
- `"Add a size.md action to the playground silo"`
- `"Show me how the action inheritance works between base and circle"`

### Pattern Usage
- `"Use the validate pattern to add input validation to a new action"`
- `"Create a test action for circle using the test-shape template"`
- `"Add a new pattern for logging action execution"`

### Advanced Operations
- `"Create a composite action that uses all three: color, shape, and size"`
- `"Debug why an action isn't found in a specific silo"`
- `"Add a new silo README with specific constraints and test inheritance"`