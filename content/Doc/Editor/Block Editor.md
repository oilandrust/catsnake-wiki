# Block Editor

Tools for placing and editing wall blocks in level scenes. Blocks are `LevelWall` entities parented under `LevelRoot`.

## Opening the Block Tool

- **Shortcut:** `Ctrl+B` (Scene View must be focused)
- **Menu:** Cat Snake → Toggle Block Tool
- **Inspector:** Select `LevelRoot` → **Open Block Tool**
- **Scene View palette:** Select the **Block** tool in the toolbar

The tool only works in scenes that contain a `LevelRoot`.

## Placing Blocks

With the Block tool active:

1. **Click** to place a 1×1×1 wall at the grid cell under the cursor.
2. **Click and drag** to place a multi-cell block. A blue wireframe preview shows the footprint while dragging.
3. **Shift + drag** locks the drag to one axis (whichever direction you move first).
4. **Esc** cancels an in-progress drag.

Each placed block is instantiated from `Assets/Prefabs/Level/Wall.prefab` and named automatically, for example:

- `Wall (2, 1, 4)` — single cell
- `Wall (0, 1, 0) [3x2x1]` — multi-cell block

### Y Placement

While the Block tool is active, a small panel in the top-left of the Scene View controls vertical placement:

| Control | Description |
|---------|-------------|
| **Snap Y from ray hit (stack on top)** | When enabled (default), the cursor ray hits existing wall geometry and places the new block on top of the wall column at the hit X/Z. |
| **Y plane** | When snap is disabled, placement uses a fixed horizontal plane at this Y value. |

Ray hits against walls use wall bounds (walls have no colliders). If nothing is hit, the tool falls back to physics raycasts or the Y plane.

## Editing Existing Blocks

Select a wall and use the **Move tool (W)** in the Scene View.

### Move

Drag the position handle to move the block’s **anchor** (minimum-corner cell) to a new grid position.

### Resize

- **Face handles:** With the Move tool active, blue dot handles appear on each face. Drag a handle to extrude or shrink that face.
- **Inspector:** On the `LevelWall` component, use the **X / Y / Z** +/- buttons to change size one cell at a time. Shrinking from the positive side moves the anchor so the opposite face stays fixed.

### Grid Size

- **Anchor** — minimum-corner cell `(x, y, z)`.
- **Grid Size** — number of cells the wall occupies in each axis (minimum 1).
- **Max Corner** — anchor + size − 1.

The visual mesh is generated per block with tiled UVs so outline textures repeat per cell.

## LevelRoot Inspector

On `LevelRoot`:

- **Add Wall Block** — places a 1×1 wall at a suggested anchor (level bounds center, or `(0, 1, 0)` if no bounds).
- **Open Block Tool** — activates the Block tool.

The inspector also shows a level summary (wall count, multi-cell walls, etc.) and draws the level bounds gizmo in the Scene View.

## Overlap Warnings

When moving or resizing a wall, overlapping occupied cells are highlighted in the Scene View so you can spot collisions with other walls or entities.

## Related Files

| File | Role |
|------|------|
| `Assets/Scripts/Editor/LevelBlockTool.cs` | Block placement tool |
| `Assets/Scripts/Editor/LevelWallEditor.cs` | Move / extrusion handles, inspector size controls |
| `Assets/Scripts/Editor/LevelRootEditor.cs` | LevelRoot buttons and bounds gizmo |
| `Assets/Scripts/Editor/GridEditorUtility.cs` | Grid snap, raycast, overlap drawing |
| `Assets/Scripts/Level/LevelWall.cs` | Wall entity and visual mesh update |
| `Assets/Scripts/Level/WallBlockMeshBuilder.cs` | Generated box mesh with per-cell UVs |
