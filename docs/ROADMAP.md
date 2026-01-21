# NekoAR Roadmap (v0.1 / v0.2)

> This document summarizes the agreed scope and product/technical decisions for NekoAR.

## Project goals
- Provide an ARCore-based **2D character photo** experience.
- Community-friendly: simple **character pack** format, clear licensing metadata.
- Realism-oriented: plane-only placement, proper world scaling, per-character shadow.

## Target platforms
- **minSdk:** 29 (Android 10)
- Primary test target: Android 14 (typical mainstream)
- target/compileSdk: follow latest stable Android SDK
- License: MIT

## Core product principles
- **Plane-only placement:** characters can be placed only on detected planes.
- **Billboard rendering:** the character always faces the camera.
- **Single-instance per character ID:** in one scene/photo, there can be multiple characters, but **no duplicate `characterId`**.
- **High-res export:** photo export should be as high resolution as practical, and exported media should not include UI.

## Interaction & editing
- Tap a plane to place a character (or focus an existing one if duplicate).
- Tap a character to select it (focus).
- Transform gestures (selected character):
  - Drag: move along plane
  - Pinch: scale
  - Rotate: rotate around vertical axis
- **Lock** (per character): prevents accidental transforms.
- **Z-order / layering:** overlapping selection defaults to the topmost character; provide bring forward/backward controls.

## Undo/Redo rules (v0.1)
Undo/Redo is a single linear timeline (Y):
- Included:
  - Place / Delete
  - Transform (move/scale/rotate)
  - Pose switch
  - Skin switch
  - Shadow toggle (per character)
  - Layering (z-order) changes
- Excluded:
  - Lock (does not need to be restored)
  - Global UI settings (grid/level/timer, plane visualization toggle)

## Character Pack v1
### Package format
- Import as **ZIP**.
- Import methods:
  - File picker import
  - Auto-scan a designated directory

### Identity & duplication
- `characterId` is defined by `character.json` -> `id`.
- Duplicate rule: a scene cannot contain two instances with the same `id`.

### Skins (S1)
- Multiple skins/outfits per character.
- Skin can be switched **after placement**.
- When switching skins:
  - If the new skin contains the same pose name, keep the pose.
  - Otherwise, fall back to `defaultPose`.

### Poses (v0.1)
- v0.1 is **Pose-only** (no actions).
- Pose types:
  - `png_single`: a single PNG frame
  - `png_sequence`: looping PNG sequence with configurable fps

### World scale calibration
- `metersTall` may be missing in the pack.
- Provide an in-app scale calibration slider.
- Save calibration **locally** per `characterId` (Cal1).

### Licensing metadata
- Require displaying `author`, `source`, `license` in character details.
- If missing, show a warning (still allow use if you choose, but clearly marked as non-compliant).

## UI (v0.1)
- Default UX is camera-first (Ux2).
- Bottom **Pose Bar** (A1): horizontally scrollable, **text-only** (PBar1).
- Per-character controls (UI1, floating toolbar):
  - Delete
  - Lock
  - Shadow toggle
  - Skin selector
  - Layering controls
- Photo tools:
  - Countdown timer
  - Grid lines
  - Level/horizon indicator

## Rendering & realism
- Per-character **ground shadow** (default ON), toggleable.
- Smoother edges / anti-aliasing strategy (implementation-defined).
- Performance constraints accepted (frame count / image size limits in docs).

---

# Milestone v0.1 (MVP: Pose Photo)

## In scope
1. ARCore session bootstrap & capability checks
2. Plane detection and visualization
3. Placement on planes + anchors
4. Billboard quad rendering
5. Character pack import (ZIP) + directory scan
6. Pose playback (PNG single + PNG sequence)
7. Skins (switch after placement)
8. Per-character shadow toggle
9. Layering (z-order) controls + topmost hit testing
10. Undo/Redo (rules above)
11. Lock (per character)
12. High-resolution photo export (no UI)
13. Photo helpers: countdown/grid/level
14. Licensing metadata display + warnings
15. Basic error handling (missing files, invalid JSON, etc.)

## Not in scope
- Actions
- Dynamic photo/video recording
- Animated WebP animation source
- Advanced occlusion/depth-based hiding

---

# Milestone v0.2 (Actions + Dynamic Photo + Animated WebP)

## In scope
### 1) Actions
- Action list UI (paired with Pose UI)
- Trigger actions on selected character
- Restart-on-trigger behavior
- Return-to-pose behavior

### 2) Dynamic Photo (Animated WebP export)
- Export an **Animated WebP** "dynamic photo":
  - Default duration: **3 seconds**
  - Output fps: **24 fps**
  - No UI in output
  - Support countdown
  - Recording disables editing/transforms during capture
  - Save to system gallery (DCIM/Pictures)
- Capture modes (C):
  - Record current playback
  - Or auto-trigger a chosen action then record

### 3) Animated WebP as an animation source
- Allow character packs to specify Animated WebP files for poses/actions (as a FrameSource alternative to PNG sequences).

## Not in scope (unless later decided)
- Full video recording workflows
- Live2D/Spine integration (reserved for future optional plugins)
