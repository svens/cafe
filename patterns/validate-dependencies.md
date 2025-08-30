---
description: Validate that all action dependencies exist before execution
inputs: ["action_file"]
outputs: "validation results and missing dependencies"
ai_notes: Use this pattern to check dependencies field in action YAML frontmatter
command: |
  ACTION_FILE="$1"
  if [[ ! -f "$ACTION_FILE" ]]; then
    echo "Error: Action file $ACTION_FILE not found" >&2
    exit 1
  fi
  
  # Extract dependencies from YAML frontmatter
  DEPS=$(grep -A 10 "^dependencies:" "$ACTION_FILE" | grep -E "^\s*-\s*" | sed 's/^\s*-\s*//' | tr -d '"[],' | grep -v "^$")
  
  if [[ -z "$DEPS" ]]; then
    echo "No dependencies declared for $(basename "$ACTION_FILE")"
    exit 0
  fi
  
  MISSING=""
  for dep in $DEPS; do
    FOUND=false
    
    # Check local .cafe/actions/
    if [[ -f ".cafe/actions/${dep}.md" ]]; then
      FOUND=true
    fi
    
    # Check parent directories
    DIR="$(pwd)"
    while [[ "$DIR" != "/" ]]; do
      if [[ -f "$DIR/.cafe/actions/${dep}.md" ]]; then
        FOUND=true
        break
      fi
      DIR="$(dirname "$DIR")"
    done
    
    # Check base framework
    if [[ -f "shape/.cafe/actions/${dep}.md" ]]; then
      FOUND=true
    fi
    
    if [[ "$FOUND" == "false" ]]; then
      MISSING="$MISSING $dep"
    fi
  done
  
  if [[ -n "$MISSING" ]]; then
    echo "Error: Missing dependencies for $(basename "$ACTION_FILE"):$MISSING" >&2
    exit 1
  else
    echo "All dependencies validated for $(basename "$ACTION_FILE")"
  fi
---

# Dependency Validation Pattern

This pattern validates that all declared dependencies exist before executing an action.

## Usage

```bash
# Validate dependencies for a specific action
source patterns/validate-dependencies.md
validate_dependencies ".cafe/actions/draw.md"

# In action files, add to command:
# source ../../patterns/validate-dependencies.md
# validate_dependencies "$0"
```

## How It Works

1. Extracts `dependencies:` array from YAML frontmatter
2. For each dependency, follows Action Resolution Algorithm:
   - Check local `.cafe/actions/{dep}.md`
   - Traverse up directory tree
   - Check base framework `shape/.cafe/actions/{dep}.md`
3. Reports any missing dependencies
4. Exits with error code if dependencies missing

## Integration

Add this validation to composite actions like `draw.md` to ensure all required actions exist before execution.