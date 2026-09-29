# Proposal: Save as Location / Move to Location

## Status

Concept proposal.

## Intended integrations

- Rolodex
- AEXIS

This should be implemented as a reusable file-routing primitive that both systems can consume rather than a one-off behavior owned by either product.

## Core idea

Windows Explorer should allow a user to save any directory as a reusable named **Location**.

Once a Location has been saved, the user can select one file or many files anywhere in Explorer and move that selection to the saved destination without navigating back to the folder manually.

The essential interaction is:

`Right-click folder → Save as Location`

Then later:

`Select files → Right-click → Move to Location → <saved location>`

The user discovers a destination once, saves it once, and can subsequently route files there from anywhere.

## Example

A user navigates to:

`D:\AEXIS\Worlds\Earth\References\`

They right-click the directory and choose:

`Save as Location`

They name it:

`AEXIS Earth References`

Later, while viewing Downloads, Desktop, Screenshots, or another directory, they select dozens or hundreds of files and use:

`Right-click → Move to Location → AEXIS Earth References`

The selected files are moved directly into the saved directory without opening a destination picker or manually navigating the folder tree.

## Saved Locations pop-out

The saved-location system should not be limited to a long Windows submenu.

As the user's saved Locations grow, **Move to Location** should be able to open a compact pop-out interface containing the user's saved destinations.

Conceptually:

```
Right-click selected files
    ↓
Move to Location
    ↓
[ Saved Locations pop-out ]

AEXIS Earth References
AEXIS Textures
Rolodex Inbox
Rolodex Archive
Recent Locations
Search...
Manage Locations...
```

The pop-out becomes a reusable **destination palette**.

It should eventually support:

- Search/filter as the location list grows.
- Recently used Locations.
- Pinned/favorite Locations.
- Product grouping, such as Rolodex and AEXIS.
- Icons or small destination identifiers.
- Keyboard navigation.
- Fast selection without reopening Explorer.
- A management view for rename, reorder, pin, remove, and inspect.

The Windows context menu therefore remains lightweight while the richer saved-location interface can scale independently.

## Why this exists

Traditional file movement repeatedly asks the user to rediscover the destination.

This proposal separates two actions:

1. **Destination discovery** — performed once when the user saves a Location.
2. **File routing** — performed repeatedly afterward from wherever the files currently are.

A frequently used directory becomes an action rather than a place the user must repeatedly navigate to.

## Saving a destination

When the user right-clicks a directory, expose:

`Save as Location`

The system stores a reusable Location record.

Suggested minimum fields:

- Stable ID
- Display name
- Local path
- Destination type
- Associated integration(s)
- Date created
- Last used date
- Optional icon
- Optional description
- Pinned/favorite state

The folder name can be the default display name, but the user should be able to rename the saved Location without renaming the actual directory.

## Moving selected files

When one or more filesystem items are selected, expose:

`Move to Location`

Depending on UX mode, that command may either:

1. Show a small submenu of the most relevant/recent Locations, or
2. Open the Saved Locations pop-out for the full destination set.

Example compact submenu:

```
Move to Location >
    AEXIS Earth References
    AEXIS Textures
    Rolodex Inbox
    --------------------
    More Locations...
    Manage Locations...
```

Choosing **More Locations...** opens the pop-out destination palette.

## Move semantics

The command is explicitly **Move to Location**, not Upload to Location.

For a normal local filesystem destination, it should behave like a conventional file move:

- On the same volume, use the operating system's efficient move/rename behavior when possible.
- Across volumes, use a safe copy-then-remove workflow.
- A failed transfer must not silently delete the source.
- Partial failures must be surfaced clearly.
- Existing-name collisions need an explicit conflict policy.
- The operation should work for one item or large multi-selections.

## Location abstraction

The first implementation can target local directories, but the data model should not assume that a Location is permanently equivalent to a Windows path.

A Location should conceptually be:

`Location = named reusable destination`

That allows future destination types while preserving the same interaction vocabulary.

Potential future destination types include:

- Local directory
- Network share
- Project workspace
- Rolodex-managed collection
- AEXIS asset destination
- Cloud-backed destination
- Remote storage prefix

The action presented to the user can remain **Move to Location** while the provider-specific implementation determines how the move is performed.

## Rolodex role

Rolodex can own or expose the saved-location registry as part of its broader indexing/reference model.

Possible responsibilities:

- Store Location records.
- Resolve stable identifiers to current destinations.
- Track recent/frequent use.
- Provide search and organization.
- Surface Locations to other applications.
- Allow an agent to refer to a destination by stable name/ID rather than a fragile raw path.

This creates a useful bridge between a human-friendly Windows workflow and an agent-friendly destination model.

## AEXIS role

AEXIS can consume Locations for recurring asset and project workflows.

Examples:

- World references
- Textures
- Models
- Audio
- Maps
- Character assets
- Project imports
- Screenshots
- Generated outputs

An AEXIS workspace could register relevant directories as Locations so users can route assets into a world or project directly from Explorer.

## Design principle

The feature should feel like teaching the operating system:

> **When I say this name, I mean this destination.**

After that association exists, moving files should require selection and intent, not repeated navigation.

## Initial implementation boundary

Version 1 should prove the smallest useful loop:

1. Register a local folder with **Save as Location**.
2. Persist its stable Location record.
3. Select one or more files.
4. Invoke **Move to Location**.
5. Choose a saved Location.
6. Safely move the files.
7. Surface success, conflicts, or failures.

The pop-out destination palette can then become the scalable primary picker as the system grows.
