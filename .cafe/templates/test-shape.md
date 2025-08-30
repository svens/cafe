---
description: Template for testing shape implementations (extend in silos)
inputs: ["shape_type"]
outputs: "test results for shape functionality"
dependencies: ["color", "shape", "draw"]
ai_notes: Silos should extend this template to create their own test-{shape}.md actions
command: |
  SHAPE_TYPE="${1:-unknown}"
  echo "Testing $SHAPE_TYPE shape..."
  
  # Test individual actions
  echo "Color test: $(bash -c "$(grep '^command:' .cafe/actions/color.md | sed 's/^command: *//')" 2>/dev/null || echo "MISSING")"
  echo "Shape test: $(bash -c "$(grep '^command:' .cafe/actions/shape.md | sed 's/^command: *//')" 2>/dev/null || echo "MISSING")"
  
  # Test composite action
  echo "Draw test: $(bash -c "$(grep '^command:' .cafe/actions/draw.md | sed 's/^command: *//')" 2>/dev/null || \
                 bash -c "$(grep '^command:' ../../shape/.cafe/actions/draw.md | sed 's/^command: *//')" 2>/dev/null || echo "MISSING")"
  
  echo "$SHAPE_TYPE shape test completed"
---

# Shape Test Template

This template provides a standardized way to test shape implementations across silos.

## Usage in Silos

Create `test-{shape}.md` in your silo that extends this template:

```yaml
# In silo/circle/.cafe/actions/test-circle.md
command: |
  source ../../../.cafe/templates/test-shape.md
  test_shape "circle"
```

## Test Coverage

- Individual action availability (color, shape)
- Composite action functionality (draw)  
- Action inheritance/override verification
- Basic error handling for missing actions