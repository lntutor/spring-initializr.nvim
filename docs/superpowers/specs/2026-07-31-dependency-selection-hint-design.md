# Dependency Selection Hint Design

## Goal

Help users discover how to add dependencies when the Selected Dependencies panel is empty, without changing the populated dependency-card view.

## Design

The empty-state renderer in `dependencies_display.lua` will return two lines: the existing `No dependencies selected` status and a concise instruction to press `<CR>` on the Add Dependencies control. The instruction lives in the panel where the missing selection is visible, so it is contextual and disappears automatically when dependency cards are rendered.

Alternatives considered were permanently adding the shortcut to the button title and showing a transient notification when the panel receives focus. The panel hint is preferred because it satisfies the issue's empty-only requirement without adding persistent visual noise or relying on a notification the user may miss.

## Testing

A focused Plenary spec will exercise the public `update_display()` behavior with a real Neovim buffer. It will verify that an empty selection writes both the status and instruction, then add a dependency and verify that the instruction is absent from the rendered card output. The full existing Plenary suite and lint/format checks will run afterward.

## Scope

No picker behavior, key mapping, dependency-card layout, or configuration surface changes are included.
