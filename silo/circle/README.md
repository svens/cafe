# Circle Silo

This silo implements circular shapes with specific color and behavior requirements.

## Silo-Specific Rules

- **Color requirement**: All circles must be red (overrides base colorless)
- **Shape identity**: Always returns "circle" as shape type
- **Validation**: Circle-specific actions should validate roundness properties

## Action Overrides

- `color.md` - Returns "red" instead of base "colorless"
- `shape.md` - Returns "circle" instead of base "shape"

## Usage Context

When working in this silo, AI should:
1. Apply main README.md rules first
2. Then apply these circle-specific constraints
3. Ensure all new circle actions maintain red color consistency
4. Consider roundness/radius properties for future extensions

## Future Extensions

Consider adding:
- `radius.md` action for circle-specific properties  
- `area.md` action using π × radius² calculation
- `circumference.md` action using 2π × radius