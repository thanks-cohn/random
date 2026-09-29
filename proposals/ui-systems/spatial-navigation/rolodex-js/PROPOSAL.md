# Proposal: Rolodex.js as a Spatial Navigation System

## Status

Project direction proposal.

## Canonical implementation

`thanks-cohn/rolodex.js`

Current baseline: **v0.1**

## Summary

Rolodex.js should develop from its current interaction prototype into a small, reusable **spatial navigation system for software interfaces**.

Its defining idea is not simply “a different menu.”

Its defining idea is that navigation history, branches, depth, and useful destinations can remain physically understandable without forcing the interface to keep every panel permanently open.

Traditional nested menus repeatedly destroy context:

- a submenu replaces the thing before it;
- deep paths become difficult to remember;
- returning to a useful branch requires retracing the route;
- hierarchy is logically present but visually temporary.

Rolodex.js proposes a different vocabulary:

- paths leave traces;
- branches can become visible rails or tabs;
- useful positions can be preserved;
- deep navigation can compress without becoming forgotten;
- old depth can be reopened spatially;
- interface geometry can be inspected mathematically by both humans and software agents.

The result should feel less like traversing disposable menus and more like moving through a small navigable structure.

## Existing v0.1 foundation

The current Rolodex.js prototype already establishes the important primitives:

- pop-out menu trails;
- vertical Rolodex-style navigation rails;
- starred/bookmarked path positions;
- bounded top/bottom rail windowing;
- deep-stack compression;
- a three-card depth lens;
- deterministic slot-based geometry;
- interaction overlays;
- programmatic geometry/debugging APIs;
- user settings for motion and depth behavior.

These should be treated as the **baseline vocabulary**, not temporary demo tricks to be discarded during modularization.

## Core design principle

> **Depth should remain legible without remaining fully expanded.**

Rolodex.js should preserve a user's sense of:

- where they are;
- how they got there;
- what neighboring branches exist;
- which places they intentionally saved;
- how to return to them quickly.

The system should compress visual complexity without erasing navigational memory.

## Why this should become a library

Many applications independently rebuild some version of:

- nested menus;
- expandable trees;
- breadcrumbs;
- tabs;
- recently visited locations;
- favorites;
- command palettes;
- stacked inspectors;
- contextual flyouts.

These systems are usually implemented independently even though they are all representations of navigation state.

Rolodex.js can provide a shared state and geometry model for these interactions while allowing applications to choose their own rendering style.

It should not try to become a full application framework.

The goal is closer to:

`spatial navigation primitives + geometry + persistence + adapters`

than:

`replace React/Vue/Svelte`.

## Proposed architecture

Rolodex.js should eventually separate into several layers.

### 1. Core state model

Framework-independent logical navigation state.

Responsibilities:

- nodes;
- parent/child relationships;
- active path;
- branch history;
- saved/starred positions;
- recent positions;
- collapsed depth;
- navigation transitions;
- stable IDs.

The core should not require DOM rendering.

### 2. Geometry engine

Deterministic layout contracts for Rolodex structures.

Responsibilities:

- rail slots;
- tab positions;
- trail geometry;
- compression thresholds;
- depth-lens placement;
- collision relationships;
- visible-tail rules;
- anchor relationships.

Geometry should remain inspectable rather than existing implicitly inside CSS.

### 3. Interaction model

Reusable behavior for:

- hover;
- click;
- keyboard traversal;
- branch opening;
- path preservation;
- starring;
- restoring;
- temporary versus persistent expansion;
- interaction overlays and occlusion.

### 4. Persistence

Optional storage for saved navigation state.

Potential persisted state:

- starred branches;
- saved paths;
- recent paths;
- pinned rails;
- custom labels;
- ordering;
- per-application preferences.

Persistence must be separable from the core so applications may use local storage, files, databases, synced state, or no persistence at all.

### 5. Render adapters

The state/geometry model should be usable through:

- vanilla JavaScript;
- React;
- Vue;
- Svelte;
- Web Components or other adapters where useful.

Adapters should translate Rolodex state into framework-native rendering rather than duplicating the navigation logic.

### 6. Inspection and agent interface

Programmatic inspectability is a first-class feature.

A programmer or agent should be able to ask:

- What path is open?
- Which branch owns this tab?
- What slot should this element occupy?
- What is occluding this region?
- Which items are currently compressed?
- Which saved positions exist?
- What would opening this node do?
- Does rendered geometry match expected geometry?

The existing debugging philosophy should therefore graduate into a stable inspection contract rather than being removed as development scaffolding.

## The Rolodex vocabulary

The library should establish consistent concepts.

### Trail

The currently traversed path through nested navigation.

### Branch

A navigable divergence from a path.

### Rail

A bounded spatial surface containing preserved navigation positions.

### Tab

A compressed visible representation of a preserved path position or branch.

### Saved Position

A user-intentionally preserved point in navigation.

### Depth Stack

Historical or nested depth that cannot remain fully expanded.

### Depth Lens

A temporary inspection surface showing a readable slice of compressed depth.

### Overlay

An interaction region that can modify hit-testing or behavior without requiring equivalent visual geometry.

### Location

A named destination external to or represented by an application.

A Location can be surfaced through Rolodex without Rolodex itself owning the underlying resource.

## Saved Locations integration

The separate **Save as Location / Move to Location** proposal is a strong real-world consumer of Rolodex.js.

Rolodex.js should be able to render a Saved Locations collection as a spatial destination picker.

Example:

```
Move to Location
      ↓
┌ Saved Locations ┐
│ AEXIS            │
│   Earth          │
│   Textures       │
│   Models         │
│                  │
│ Rolodex          │
│   Inbox          │
│   Archive        │
└──────────────────┘
```

As the user moves deeper, Rolodex trails and rails can preserve the destination hierarchy without forcing a giant traditional submenu.

Important separation of responsibility:

- the **Location system** owns destination records and move semantics;
- **Rolodex.js** owns the spatial navigation experience used to browse and select them.

This keeps Rolodex reusable beyond file management.

Related proposal:

`proposals/file-management/windows-shell/location-routing/move-to-location/PROPOSAL.md`

## AEXIS integration

AEXIS is another natural consumer.

Possible Rolodex surfaces include:

- world hierarchy;
- project navigation;
- asset libraries;
- editor tool families;
- object relationships;
- linked planes/islands;
- building interiors;
- scene history;
- saved editor positions.

A deeply nested world editor benefits from preserving spatial navigation history because users repeatedly move between related branches rather than following a single linear hierarchy.

Rolodex should therefore remain general enough that AEXIS can adopt it without embedding AEXIS-specific assumptions into the library.

## Human and agent parity

One of the strongest directions for Rolodex.js is that the same UI model should be understandable by both people and software agents.

A human sees:

- rails;
- tabs;
- cards;
- branches;
- depth.

An agent sees:

- stable node IDs;
- relationships;
- geometry;
- state;
- expected layout;
- actions.

Both should be referring to the same underlying structure.

This reduces the need for an agent to infer interface state from screenshots alone.

## Proposed package direction

When the prototype is ready to split, a possible package structure is:

```
@rolodex/core
@rolodex/geometry
@rolodex/dom
@rolodex/react
@rolodex/vue
@rolodex/svelte
@rolodex/inspect
```

This is a direction, not a requirement for the next version.

The smallest viable extraction should happen first.

## Normal and AI-oriented distributions

Rolodex.js should also be a candidate for the broader idea of maintaining both:

- a conventional developer-facing distribution;
- an AI/agent-oriented distribution or metadata layer.

The AI-oriented form should **not fork the behavior of the library**.

Instead, it can expose richer machine-readable information:

- explicit contracts;
- stable action names;
- navigation schemas;
- geometry descriptions;
- state inspection;
- examples optimized for agents;
- machine-readable capability metadata.

Conceptually:

```
rolodex.js
rolodex-ai.js
```

could represent two interfaces to the same underlying system rather than two incompatible implementations.

This deserves its own implementation proposal before becoming a package commitment.

## Accessibility

Spatial novelty cannot come at the cost of navigability.

Before Rolodex.js is treated as production-ready, it should establish:

- complete keyboard traversal;
- deterministic focus behavior;
- screen-reader semantics;
- reduced-motion behavior;
- non-hover access to every action;
- sufficient target sizes;
- predictable escape/back behavior.

Accessibility behavior should be part of the core interaction contract rather than patched onto individual adapters.

## Performance

The library should remain appropriate for low-resource devices.

Important properties:

- bounded DOM growth;
- bounded visible rail history;
- deterministic layout work;
- no requirement for a heavy framework;
- no animation requirement for correctness;
- graceful reduced-motion mode;
- lazy rendering for deep histories.

A long logical path must not imply an equally long physical interface.

That principle is already present in the v0.1 compression and windowing model and should remain fundamental.

## Version direction

### v0.1

Approved interaction baseline.

### v0.2

Focus on internal boundaries without changing the basic interaction language.

Potential work:

- separate state from rendering;
- clean historical API names;
- formalize node/path IDs;
- formalize geometry inspection;
- add stronger keyboard behavior;
- introduce serialized saved-state format.

### v0.3+

Potential extraction of the first reusable package.

Only after the behavior is stable should framework adapters proliferate.

## Non-goals

Rolodex.js does not need to become:

- an operating system;
- a full application framework;
- a React replacement;
- a filesystem;
- an AEXIS-specific library;
- a Rolodex-themed visual skin that applications must copy exactly.

The reusable asset is the **navigation model**, not one particular visual treatment.

## Success criteria

Rolodex.js is succeeding when an application can represent a complex nested structure with it and the user can go deep without losing orientation.

From the developer side, success means the application can inspect and control that navigation state through explicit contracts rather than reverse-engineering visual layout.

From the agent side, success means an automated system can understand and manipulate the same navigation graph without relying primarily on pixel interpretation.

## Central thesis

Rolodex.js should become a small spatial-navigation language for interfaces.

Its value is not that menus pop out in an unusual direction.

Its value is that **navigation leaves useful structure behind**.
