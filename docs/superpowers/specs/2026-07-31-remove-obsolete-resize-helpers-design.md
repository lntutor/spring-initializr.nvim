# Remove Obsolete Resize Helpers Design

## Goal

Remove the unreachable pre-autocmd-manager resize implementation that leaves upstream Luacheck failing.

## Design

Delete `close_for_resize()` and `reopen_after_resize()` from `ui/init.lua`, along with the `events` import used only by that dead path. The live resize behavior remains in `activate_ui()`, which delegates registration and cleanup to `autocmd_manager` and reopens through the public `M.close()`/`M.setup()` flow.

Keeping the old helpers suppressed with a Luacheck annotation would hide dead code. Reconnecting the old recursive path would duplicate the manager-based implementation. Removal is the smallest consistent cleanup.

## Testing

Luacheck provides the regression: upstream starts with W211 for the unused recursive helper and must finish with zero warnings. The full Plenary suite, StyLua, Selene, and diff checks guard behavior and hygiene.
