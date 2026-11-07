## 2024-11-05 - Global Cache for Colab Models
**Learning:** In Colab notebooks, repeatedly running a pipeline cell that initializes a large machine learning model (like Hunyuan3D pipeline via `.from_pretrained()`) can be a significant performance bottleneck.
**Action:** When working on Colab notebooks, utilize the `globals()` state to cache heavy object initializations. This avoids redundant disk and VRAM loading during rapid iteration or consecutive cell runs.

## 2025-02-17 - Avoid Stale State Bugs in Colab I/O Optimizations
**Learning:** In a Jupyter/Colab notebook, optimizing away a `trimesh.load(file)` call by just checking if a generic `mesh` variable is `in locals()` is extremely dangerous. It breaks cell idempotency because `mesh` will persist across out-of-order executions, meaning the code will process old, stale data instead of the newly generated file.
**Action:** When bypassing I/O for performance in a notebook, introduce a dedicated, highly specific intermediate variable (e.g., `raw_synthesis_mesh`) during the generation step, and use *that* specific variable for the shortcut in subsequent steps. Always include a fallback to disk-loading for safe out-of-order execution, and ensure the fallback accurately replicates the implicit type-casting of the disk load (like flattening a `trimesh.Scene` into a single mesh).

## 2025-02-17 - Avoid Deep Copying Large Meshes
**Learning:** In a Jupyter/Colab notebook, deeply copying a large mesh object to avoid side-effects is inefficient and uses excess RAM and time.
**Action:** When passing a mesh object between processing steps in a notebook cell, pass it directly without `.copy()`, as subsequent operations will operate on the correct object. Explicitly track variables at the cell execution scope and clear tracking variables (e.g. `_current_exec_mesh = None`) to prevent reuse and stale state bugs, rather than relying on deep copies.
