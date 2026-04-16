<p align="center">
  <img src="./assets/campello_logo_git.png" height="240" alt="Campello logo" />
</p>

*A modular, composable game engine inspired by declarative design.*

Campello is a modular and multiplatform, cpp20 low-level game engine built as a collection of independent libraries.

Instead of a monolithic architecture, Campello is designed as a **composable system**, where each subsystem (rendering, audio, input, UI, assets) evolves independently but integrates seamlessly.

---

## 🧠 Philosophy

- 🧩 **Modular by design**  
  Each system is a standalone library.

- 🔗 **Composable architecture**  
  Systems are designed to be combined, not hidden behind a black box.

- 🔓 **Open core**  
  All runtime modules are public and reusable.

- 🛠️ **Editor-driven development**  
  A private editor validates real-world usage and guides API design.

---

## 📦 Modules

### 🎨 [campello_renderer](https://github.com/rusoleal/campello_renderer - 3D Rendering
3D rendering pipeline on top of [campello_gpu](https://github.com/rusoleal/campello_gpu) based on [gltf spec](https://www.khronos.org/gltf/).

---

### 🧩 [campello_widgets](https://github.com/rusoleal/campello_widgets) - UI / Widgets
Declarative-style UI system inspired by modern frameworks. Built with [campello_gpu](https://github.com/rusoleal/campello_gpu).

---

### 🔊 [campello_audio](https://github.com/rusoleal/campello_audio) - Audio
Audio playback and processing.

---

### 🎮 [campello_input](https://github.com/rusoleal/campello_input) - Input
Unified input handling.

---

### 📦 [campello_gpu](https://github.com/rusoleal/campello_gpu) - Graphics abstraction
Gpu pipelines abstraction based on webgpu.

---

### 📦 [gltf](https://github.com/rusoleal/gltf) - Gltf 2.0 asset loader
glTF 2.0 parsing and asset integration.

---

### 📦 [campello_image](https://github.com/rusoleal/campello_image) - Image asset loader
Image loader.

---

### 📦 [vector_math](https://github.com/rusoleal/vector_math) - Vector/Matrix helper
Multiplatform vector and matrix helper.

---


## 📈 Progress

### Current Focus
- Rendering pipeline evolution
- Widget system maturity
- Engine/editor integration

### Planned
- Scene system
- Resource management
- ECS / composition model

---

## 🧭 Roadmap

A public roadmap will be available via GitHub Projects.

---

## 💬 Devlog

Development updates will be shared using GitHub Discussions.

---

## ⚠️ Status

Campello is an experimental project under active development.  
Expect breaking changes.