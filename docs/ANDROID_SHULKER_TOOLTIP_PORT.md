# Android Shulker Box Tooltip port

Status: engineering kickoff / API audit. No installable mod or APK is claimed by this commit.

## Target

Implement a ShulkerBoxTooltip-style inventory preview for Horizon Android using the Horizon mod SDK and the Android game host. The implementation must not depend on a particular handset or screen density. It should work on every Android ABI that the Horizon Android build actually ships and tests.

## Existing SDK capabilities verified on `android`

The current `crates/horizon-mod-sdk/wit/horizon.wit` exposes:

- `browser::event::key`: a registered key pressed while the pointer is over an inventory-screen slot or the recipe browser; it includes the item id, count and vanilla component string.
- `input::register-key`: configurable mod key bindings.
- `render::set-hud` and `render::set-corner`: game-rendered item icons, sprites, text and fills.
- The official `mods/official/recipes` mod demonstrates polling browser events from the frame hook and reading stack components.

These APIs are useful building blocks, but they do not yet provide the continuously hovered inventory stack, pointer position, hovered slot rectangle, or a key-up/hover-exit event for inventory slots. A one-shot key event is not sufficient to keep a tooltip correctly positioned and visible as the pointer moves.

## Required host/API work

1. Extend the browser/interaction interface with a read-only hover snapshot for inventory and container screens: hovered stack (item id, count, components); pointer coordinates in GUI pixels; hovered slot bounds in GUI pixels; screen kind and a stable screen/slot identity; and `None` when no inventory slot is hovered.
2. Ensure the Android host populates the same snapshot as other supported hosts. Do not rely on desktop-only native mixins.
3. Expose the data through the Rust SDK with backwards-compatible optional imports or a versioned capability. Keep the existing browser events intact.
4. Add a preview renderer that draws a vanilla-style tooltip panel anchored beside the hovered slot, clamped to the visible GUI bounds.
5. Parse the shulker container component defensively. Handle empty boxes, unknown items, item counts, malformed component data and the supported Minecraft data-component format.
6. Add configurable compact/full preview modifiers, optional item-count labels, empty-slot shading, box tint, and a lock/inspect mode only where the Android input model can support them reliably.
7. Add unit tests for component parsing, layout/clamping and malformed data; add host-level tests for hover transitions and Android input.
8. Build and validate Android packages for the ABIs enabled in the repository's Android build workflow. Do not claim physical-device testing unless it has actually been performed.

## Compatibility and scope

- Do not target Samsung A04 specifically; layout is based on GUI pixel dimensions and Android input/renderer interfaces.
- Do not substitute Starling or another Horizon fork.
- Reuse upstream Horizon mod code only after checking its licence and adapting it to the SDK/host APIs.
- The desktop `.hnmod` ShulkerBoxTooltip port is a behavioural reference, not an Android-ready binary.
- A content-only `.hmod` cannot implement this interactive tooltip by itself.

## First implementation milestone

Add the hover snapshot to the shared SDK/host contract, wire it through Android, and test that the hovered stack and slot geometry update correctly while moving across inventory slots. Only after that contract works should the preview UI be implemented on top of it.
