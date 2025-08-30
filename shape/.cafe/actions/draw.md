---
description: Draw a shape with color (composite action that calls color and shape actions)
inputs: []
outputs: "{color} {shape}"
dependencies: ["color", "shape"]
ai_notes: This action demonstrates composition by calling both color.md and shape.md actions and combining their outputs
command: |
  COLOR=$(bash -c "$(grep '^command:' color.md | sed 's/^command: *//')")
  SHAPE=$(bash -c "$(grep '^command:' shape.md | sed 's/^command: *//')")
  echo "$COLOR $SHAPE"
---

# Draw Action

This composite action draws a shape by combining color and shape information.

It calls the `color.md` and `shape.md` actions and combines their outputs into a single result.