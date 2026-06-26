I want to build a tool to build levels quickly, it should allow me to place blocks in the level editor. Blocks are boxes ex 2x2x3, aligned with the grid and snapping at grid positions, I can move them and resize them in any direction, like an extrusion. At runtime, each cell occupied by a block will be marked as wall cannot be entered, I think the LevelWall Component should work for the game runtime, since it has a footprint size. Visually, they should have the WallOutline texture repeating so that the grid unit are visible.

The Level Block Editor Tool is implemented. Here's what was added:

### Runtime / visuals

- `WallBlockMeshBuilder` — generates a box mesh with UVs that repeat once per grid cell (WallOutline shows each cell).
- `LevelWall` — uses mesh dimensions instead of scaling; adds `SetGridSize`, `SetAnchor`, `SetBounds`, and `MaxCorner`.

### Editor tools

- `GridEditorUtility` — grid snapping, drag bounds, overlap warnings, wireframe preview.
- `LevelWallEditor` — anchor move handle, 6 face extrusion handles (Move tool), per-axis +/- size in inspector, overlap warnings.
- `LevelBlockTool` — Scene View toolbar tool (cube icon):
    - Click-drag to place blocks (1×1 on click, larger on drag)
    - Shift = axis lock
    - Esc = cancel
    - Y from ray hit or fixed Y plane
- `LevelRootEditor` — "Add Wall Block", "Open Block Tool", multi-cell wall count.

### How to use

1. Open a level scene with `LevelRoot`.
2. Select `LevelRoot` → Open Block Tool, or use the Scene View tool menu.
3. Click-drag in the scene to place blocks.
4. Select a wall → use Move tool for anchor drag and blue face handles to extrude.

At runtime, each cell in a block's footprint is registered as `GridEntityType.Wall` via existing `LevelWall.GetOccupiedCells()`