## 2024-11-05 - Global Cache for Colab Models
**Learning:** In Colab notebooks, repeatedly running a pipeline cell that initializes a large machine learning model (like Hunyuan3D pipeline via `.from_pretrained()`) can be a significant performance bottleneck.
**Action:** When working on Colab notebooks, utilize the `globals()` state to cache heavy object initializations. This avoids redundant disk and VRAM loading during rapid iteration or consecutive cell runs.
