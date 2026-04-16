# 🚀 Campello

*A modular, composable game engine inspired by declarative design.*

Campello is a modular, low-level game engine built as a collection of independent libraries.

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

## 🏗️ Architecture
    [ Campello Editor (private) ]
                ↓
        [ Engine Layer ]
                ↓


---

## 📦 Modules

### 🎨 Rendering
- https://github.com/rusoleal/campello_renderer  
Low-level rendering abstraction and pipeline.

---

### 🧩 UI / Widgets
- https://github.com/rusoleal/campello_widgets  
Declarative-style UI system inspired by modern frameworks.

---

### 🔊 Audio
- https://github.com/rusoleal/campello_audio  
Audio playback and processing.

---

### 🎮 Input
- https://github.com/rusoleal/campello_input  
Unified input handling.

---

### 📦 Asset Loading
- https://github.com/rusoleal/gltf  
glTF parsing and asset integration.

---

## 🛠️ Editor

The Campello Editor is currently private.

It acts as the main integration layer for all modules and drives:
- API design
- System interoperability
- Real-world validation

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