# ScaleChanger

This is the README for the Unity Editor extension **ScaleChanger**.  
It lets you scale all selected objects in the scene by a specified factor in one operation.

---

## Overview

**ScaleChanger** is a simple tool for the Unity Editor that changes the `Transform.localScale`  
of selected GameObjects by a specified scale factor.

- Batch scale all selected objects at once
- Option to include child objects recursively
- Supports Undo (Ctrl+Z / Cmd+Z)
- Speeds up layout adjustments and prototyping in the scene

---

## Requirements

- Unity 2020 or later  
  (Because it is an Editor extension, it should work on a wide range of Unity versions.)
- Works with both 3D and 2D projects

---

## Installation

1. Create the following folder structure in your project, for example:

   ```text
   Assets/
     IGNORANZ PROJECT/
       ScaleChanger/
         Editor/
           ScaleSelectedObjectsWindow.cs
   ```

2. Place `ScaleSelectedObjectsWindow.cs` inside an **Editor** folder.  
   - Any folder named `Editor` is fine, and the exact path/name is up to you.
   - The script will be compiled automatically and added to the Unity menu.

3. After compilation finishes, the Unity menu will show **`Tools > ScaleChanger`**.

---

## Usage

1. In the scene, select the GameObjects whose scale you want to change.
   - Multiple selection is supported.
   - You can also select only the parent object in the Hierarchy and scale it.

2. From the menu, open  
   **`Tools > ScaleChanger`**  
   to show the window.

3. Configure the following options in the window:

   - **Scale Factor**
     - The scale multiplier to apply.
     - Examples: `2` → 2x, `0.5` → half, `-1` → mirrored (inverted)
     - `0` is invalid (the button is disabled to avoid errors).

   - **Include Children**
     - When ON, all child Transforms of the selected objects are also scaled recursively.
     - When OFF, only the selected objects themselves are scaled.

4. When you are ready, click **`Apply Scale To Selection`**.

5. The `localScale` of the selected objects (and, optionally, their children) will be updated all at once.

6. If you want to revert the changes, use **Ctrl+Z / Cmd+Z** to Undo.

---

## Behavior Details

- Scale Targets
  - All Transforms contained in `Selection.transforms` are used as initial targets.
  - When **Include Children** is enabled, the tool also calls  
    `GetComponentsInChildren<Transform>(true)`  
    on each selected object, collects all descendants, and removes duplicates before processing.

- Undo Support
  - The tool calls `Undo.RecordObjects(...)` before changing the scales,  
    so the operation can be undone just like any other edit in the Editor.

- Actual Changes
  - For each Transform, the tool applies:
    `t.localScale = t.localScale * scaleFactor;`

---

## Notes

- **Scale Factor 0 is invalid**  
  - If you set the factor to `0`, the objects would collapse completely,  
    so the UI shows a warning and disables the button.

- **Impact on Prefabs**
  - Scaling a prefab instance in the scene will add overrides to that instance.
  - The prefab asset itself is not changed, but if you press **Apply**,  
    the new scale will be written back to the prefab asset.

- **Non-uniform Scale**
  - If an object already has non-uniform scale (different values for x, y, z),  
    the factor is applied to each axis as it is.
  - If your game uses physics or shaders that are sensitive to non-uniform scale,  
    please verify that the result behaves as expected.

---

## FAQ

### Q. I don’t want to scale child objects. What should I do?

A. Turn **Include Children** OFF.  
   If you select only the parent in the Hierarchy with this option disabled, only the parent’s `localScale` will change.

---

### Q. I want to exclude some objects from scaling.

A. In that case, adjust your selection in the Hierarchy so that any objects you don’t want to scale are not selected.  
   If needed, you could extend this tool later with filters such as “exclude by Tag” or “only scale specific Layers”.

---

## License / Usage

- MIT License

---

## Changelog

- **v1.0.0**
  - Initial release
  - Implemented multiplying the `localScale` of selected objects and (optionally) their children
  - Undo support
