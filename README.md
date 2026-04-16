<p align="center">
  <img src="./assets/campello_logo_git.png" width="360" alt="Campello logo" />
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

### 🎨 Rendering
- https://github.com/rusoleal/campello_renderer  
3D rendering pipeline on top of [campello_gpu](https://github.com/rusoleal/campello_gpu) based on [gltf spec](https://www.khronos.org/gltf/).

---

### 🧩 UI / Widgets
- https://github.com/rusoleal/campello_widgets
Declarative-style UI system inspired by modern frameworks. Built with [campello_gpu](https://github.com/rusoleal/campello_gpu).

---

### 🔊 Audio
- https://github.com/rusoleal/campello_audio
Audio playback and processing.

---

### 🎮 Input
- https://github.com/rusoleal/campello_input
Unified input handling.

---

### 📦 Graphics abstraction
- https://github.com/rusoleal/campello_gpu

---

### 📦 Gltf 2.0 asset loader
- https://github.com/rusoleal/gltf
glTF 2.0 parsing and asset integration.

---

### 📦 Image asset loader
- https://github.com/rusoleal/campello_image


---

### 📦 Vector/Matrix helper
- https://github.com/rusoleal/vector_math


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