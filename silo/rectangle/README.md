# Rectangle Silo

This silo implements rectangular shapes with specific color and behavior requirements.

## Silo-Specific Rules

- **Color requirement**: All rectangles must be green (overrides base colorless)
- **Shape identity**: Always returns "rectangle" as shape type
- **Validation**: Rectangle-specific actions should validate width/height properties

## Action Overrides

- `color.md` - Returns "green" instead of base "colorless"
- `shape.md` - Returns "rectangle" instead of base "shape"

## Usage Context

When working in this silo, AI should:
1. Apply main README.md rules first
2. Then apply these rectangle-specific constraints
3. Ensure all new rectangle actions maintain green color consistency
4. Consider width/height properties for future extensions

## Future Extensions

Consider adding:
- `width.md` and `height.md` actions for rectangle-specific properties
- `area.md` action using width × height calculation
- `perimeter.md` action using 2 × (width + height)
- `aspect-ratio.md` action for width/height ratio