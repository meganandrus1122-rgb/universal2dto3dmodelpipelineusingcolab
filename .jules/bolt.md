## 2024-11-05 - Global Cache for Colab Models
**Learning:** In Colab notebooks, repeatedly running a pipeline cell that initializes a large machine learning model (like Hunyuan3D pipeline via `.from_pretrained()`) can be a significant performance bottleneck.
**Action:** When working on Colab notebooks, utilize the `globals()` state to cache heavy object initializations. This avoids redundant disk and VRAM loading during rapid iteration or consecutive cell runs.

## 2025-02-17 - Avoid Stale State Bugs in Colab I/O Optimizations
**Learning:** In a Jupyter/Colab notebook, optimizing away a `trimesh.load(file)` call by just checking if a generic `mesh` variable is `in locals()` is extremely dangerous. It breaks cell idempotency because `mesh` will persist across out-of-order executions, meaning the code will process old, stale data instead of the newly generated file.
**Action:** When bypassing I/O for performance in a notebook, introduce a dedicated, highly specific intermediate variable (e.g., `raw_synthesis_mesh`) during the generation step, and use *that* specific variable for the shortcut in subsequent steps. Always include a fallback to disk-loading for safe out-of-order execution, and ensure the fallback accurately replicates the implicit type-casting of the disk load (like flattening a `trimesh.Scene` into a single mesh).

## 2024-11-08 - Avoid Deep Copies of Large 3D Meshes in Colab
**Learning:** In Colab notebooks, deep copying large 3D meshes (e.g., using `.copy()`) consumes significant memory and execution time.
**Action:** When reusing in-memory objects like 3D meshes to avoid slow Google Drive I/O, safely manage state by passing the reference directly and clearing the tracking variable (e.g., `raw_synthesis_mesh = None`) immediately after to avoid unnecessary memory duplication.
