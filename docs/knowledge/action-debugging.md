# Action Debugging FAQ

Common issues and solutions when working with CAFE actions.

## Q: My action isn't found
**A:** Check action discovery order:
1. Local `.cafe/actions/` in current directory
2. Traverse up directory tree looking for base framework actions
3. Use `find . -name "*.md" -path "*/.cafe/actions/*"` to see all available actions

## Q: Action command fails with "command not found"
**A:** 
- Verify the `command:` field in YAML frontmatter is valid shell
- Check if you're in the correct directory context
- For composite actions, ensure referenced actions exist in scope

## Q: Silo inheritance not working
**A:**
- Confirm silo has proper `.cafe/actions/` structure
- Check that base framework is accessible from silo directory
- Remember: local actions override base actions completely (no merging)

## Q: How to debug composite actions like draw.md
**A:** 
- Test individual components first (`color.md`, `shape.md`)
- Add `set -x` to command for verbose output
- Check that `grep` commands find the right YAML frontmatter

## Q: Action schema validation
**A:** Ensure YAML frontmatter has required fields:
- `description:` (required)
- `command:` (required) 
- `inputs:`, `outputs:`, `ai_notes:` (recommended)