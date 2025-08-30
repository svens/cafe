---
description: Reusable validation pattern for action inputs
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

# Validation Pattern

This reusable pattern provides input validation for actions.

## Usage

```bash
# Source this pattern in other actions
source ../../patterns/validate.md
validate_input "$input" "Error: missing required input"
```

## Parameters
- `$1` - Value to validate (required)
- `$2` - Custom error message (optional, defaults to generic message)