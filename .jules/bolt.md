## 2024-11-05 - Global Cache for Colab Models
**Learning:** In Colab notebooks, repeatedly running a pipeline cell that initializes a large machine learning model (like Hunyuan3D pipeline via `.from_pretrained()`) can be a significant performance bottleneck.
**Action:** When working on Colab notebooks, utilize the `globals()` state to cache heavy object initializations. This avoids redundant disk and VRAM loading during rapid iteration or consecutive cell runs.

## 2025-02-17 - Avoid Stale State Bugs in Colab I/O Optimizations
**Learning:** In a Jupyter/Colab notebook, optimizing away a `trimesh.load(file)` call by just checking if a generic `mesh` variable is `in locals()` is extremely dangerous. It breaks cell idempotency because `mesh` will persist across out-of-order executions, meaning the code will process old, stale data instead of the newly generated file.
**Action:** When bypassing I/O for performance in a notebook, introduce a dedicated, highly specific intermediate variable (e.g., `raw_synthesis_mesh`) during the generation step, and use *that* specific variable for the shortcut in subsequent steps. Always include a fallback to disk-loading for safe out-of-order execution, and ensure the fallback accurately replicates the implicit type-casting of the disk load (like flattening a `trimesh.Scene` into a single mesh).

## 2024-03-01 - Avoid Deep Copying Large 3D Meshes in Colab
**Learning:** Deep copying large 3D meshes using `.copy()` consumes significant memory and execution time in Colab. When managing state to prevent stale data bugs, setting the variable to `None` does not remove it from the `locals()` dictionary.
**Action:** Pass the reference directly and clear the tracking variable (e.g., `var = None`) immediately after. Use `locals().get('var') is not None` instead of `'var' in locals()` for conditional checks to preserve cell idempotency.
