# Campello — Game Level Architecture Vision

> **TL;DR:** A game level is a `campello_core::World`. glTF is an *asset* format, not a *scene* format. The renderer must evolve from "draw one glTF object" to "query the ECS world and render everything."

---

## 1. The Core Misconception: glTF Is Not a Level

glTF is excellent at what it does — geometry, materials, skinning, node hierarchies, and animations — but it is fundamentally an **art delivery** format. It does not know about:

- Gameplay cameras (FoV, near/far, viewport splits, render layers)
- Engine lights (shadow settings, culling masks, baked vs. real-time flags)
- Physics colliders and rigid bodies
- Audio emitters, listeners, and reverb zones
- Spawn points, triggers, scripts, AI waypoints
- Widget overlays and UI canvases

> Even the `KHR_lights_punctual` extension is a minimal interchange spec, not a game lighting model.

**Therefore, do not try to shove an entire level into a single glTF file.** A level contains *many* entities. Some of those entities happen to reference glTF assets for their visual representation.

---

## 2. The Level Is a `World`

In `campello_core`, the `World` **is** your scene container. A level file, conceptually, is a serialized `World` that you `restore()`:

```cpp
// Conceptual level loading
World level_world;
level_world.restore(level_json);                    // Spawns entities + engine components
import_gltf_scene(level_world, "assets/hero.gltf");     // Spawns visual entities
import_gltf_scene(level_world, "assets/terrain.gltf");  // Spawns more visual entities
```

A level is a collection of entities with components. Some components are engine-specific (`Light`, `Camera`, `Collider`), and some reference external assets (`MeshRenderer` referencing a glTF mesh).

---

## 3. Separate Visual Assets from Renderable Entities

Your renderer needs to evolve from this:

```cpp
// OLD — Renderer owns the scene graph
render(gltf_scene);
```

To this:

```cpp
// NEW — Renderer consumes the ECS world
void render_system(World& world) {
    auto camera = find_active_camera(world);
    auto lights = world.query<Transform, Light>();
    auto meshes = world.query<Transform, MeshRenderer>();

    // Build command buffers from ECS data, submit to campello_gpu
}
```

The glTF loader's job is to **import** data into the ECS, not to *be* the runtime scene. When you load `hero.gltf`, you spawn entities and attach components:

- `Transform` (from glTF node matrix / TRS)
- `MeshRenderer` (holding a handle to the GPU mesh / material that came from glTF)
- `Parent` / `Children` (preserving the glTF node hierarchy)
- `Animation` (if you have a skinned mesh)

You can do this **multiple times per level**. The level geometry might come from `level_art.gltf`. The player character from `hero.gltf`. A crate from `prop_crate.gltf`. Each import spawns a subgraph of entities into the same `World`.

---

## 4. What a Level Actually Looks Like (Entity Breakdown)

Imagine a simple level. In your ECS, it is just this:

| Entity | Components |
|--------|-----------|
| **Sun** | `Transform`, `DirectionalLight { color, intensity, casts_shadows }` |
| **Lantern_01** | `Transform`, `PointLight { radius, color, intensity }` |
| **MainCamera** | `Transform`, `Camera { fov, near, far, viewport }` |
| **PlayerSpawn** | `Transform`, `PlayerStart {}` |
| **Hero** | `Transform`, `MeshRenderer { mesh: "hero.gltf#body", material: "hero_mat" }`, `Parent`, `Animation`, `RigidBody`, `CapsuleCollider` |
| **Ground** | `Transform`, `MeshRenderer { mesh: "level.gltf#terrain", material: "grass_mat" }`, `MeshCollider` |
| **AmbientAudio** | `Transform`, `AudioSource { clip: "wind.ogg", loop: true, spatial: false }` |
| **HUDRoot** | `WidgetRoot { anchor: FullScreen }`, `Canvas {}` |

Notice: **the glTF is only mentioned inside `MeshRenderer`.** It is one property among many. The level is the aggregate of all these entities.

---

## 5. glTF Node Hierarchy → ECS Hierarchy

You already have `Parent` and `Children` in `campello_core`. This **is** your scene graph. When importing a glTF:

1. For each glTF node, spawn an `Entity`.
2. Attach `Transform` with local TRS.
3. If the node has a mesh, attach `MeshRenderer { mesh_handle, material_handle }`.
4. If the node has a parent in glTF, call `world.set_parent(entity, parent_entity)`.
5. The renderer system computes world matrices by traversing `Parent` / `Children` during `PreRender`.

This means your renderer no longer cares about glTF nodes at runtime. It only cares about ECS entities with `Transform + MeshRenderer`.

---

## 6. Illumination Lives in Components, Not glTF

Since glTF does not model your lighting needs, you define engine-specific light components:

```cpp
struct DirectionalLight {
    Vec3 color;
    float intensity;
    bool cast_shadows;
    uint shadow_map_size;
};

struct PointLight {
    Vec3 color;
    float intensity;
    float radius;
};
```

The renderer system queries them:

```cpp
for (auto [transform, light] : world.query<Transform, PointLight>()) {
    // Add to light buffer for the GPU pipeline
}
```

This is exactly how modern ECS engines (Bevy, etc.) work. The renderer is **data-oriented**: it gathers everything it needs from the ECS each frame.

---

## 7. Level File Format

Because your ECS already has JSON `snapshot` / `restore`, your level format can be layered:

1. **Base layer**: A JSON world snapshot containing engine entities (lights, cameras, spawn points, empty holders).
2. **Instance layer**: A list of "prefab / glTF spawns" to execute after restore:

```json
{
  "entities": [
    { "components": { "Transform": { ... }, "DirectionalLight": { ... } } },
    { "components": { "Transform": { ... }, "Camera": { ... } } }
  ],
  "imports": [
    { "gltf": "assets/level_geometry.gltf", "parent": null, "transform": { ... } },
    { "gltf": "assets/hero.gltf", "parent": null, "transform": { ... } }
  ]
}
```

> **Note:** `World::restore()` does not preserve `Entity` IDs. Any entity references inside components (like `Parent` or a `Target` component) will need a remapping pass after restore.

---

## 8. All Systems Share the Same World

The powerful part of your ECS is that the renderer, physics, audio, and widgets all read from the same data:

| System | Queries |
|--------|---------|
| **Physics** | `Query<Transform, RigidBody, Collider>` → writes updated `Transform` |
| **Audio** | `Query<Transform, AudioSource>` + `Query<Transform, AudioListener>` |
| **Renderer** | `Query<Transform, MeshRenderer>` + `Query<Transform, Light>` + `Query<Transform, Camera>` |
| **Widgets** | `Query<WidgetRoot>` (rendered as overlay after 3D) |
| **Gameplay** | `Query<Player, Health, Transform>` |

All of these are registered in your `Schedule` at different stages. The renderer lives in `Stage::PreRender`. It does not own the scene; it **reads** the scene.

---

## 9. Migration Path for the Renderer

Your renderer currently knows how to render a single glTF object. That is actually good — you have the PBR pipeline and GPU abstractions working. Now you need to wrap it:

1. **Create renderer components** in `campello_renderer` (or a game module): `MeshRenderer`, `Camera`, `Light`, `Transform`.
2. **Build a glTF importer** that converts a loaded glTF into ECS entities + `MeshRenderer` components referencing GPU buffers.
3. **Replace the entry point**: Instead of `render(gltf)`, create a system `void scene_render_system(World&)` that queries the world and feeds renderables into your existing pipeline.
4. **Add a light accumulation step** before mesh drawing: gather `Light` components into a uniform buffer.

---

## Summary

| Concept | What It Is |
|---------|-----------|
| **Level** | A `campello_core::World` instance, populated with entities |
| **glTF** | An art asset. Import it to create entities with `MeshRenderer`. Do this many times per level. |
| **Renderer** | An ECS system. It queries `MeshRenderer`, `Light`, and `Camera` components. It does not know about glTF at runtime. |
| **Everything else** | Physics, audio, widgets — they also query the same `World`. |

You do not need to fit the game into glTF. You need to fit glTF into your ECS as one asset type among many.
