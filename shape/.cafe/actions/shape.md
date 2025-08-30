---
description: Get the type of shape (base implementation returns generic shape)
inputs: []
outputs: "shape"
ai_notes: Base shape implementation - silos should override this to provide specific shape types
command: echo "shape"
---

# Shape Action

This action returns the type of shape.

The base implementation returns "shape" but silos are expected to override this action to provide specific shape types like "circle", "rectangle", etc.