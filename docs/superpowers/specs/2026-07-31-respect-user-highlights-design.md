# Respect User Highlights Design

## Goal

Stop Spring Initializr from overwriting global Neovim highlight groups or reapplying those overrides after colorscheme changes.

## Design

Keep the internal `highlights.configure()` entry point so `ui/init.lua` and any internal callers remain compatible, but make the function intentionally non-mutating. Remove the hardcoded `NormalFloat`, `FloatBorder`, and `NuiMenuSel` writes and remove ColorScheme autocmd registration.

Deleting the module and its call sites would create a larger structural change for no user benefit. Adding opt-in color configuration would exceed the issue's acceptance criteria. A no-op compatibility boundary is the smallest change that restores ownership of global highlights to users and colorschemes.

## Testing

A focused Plenary test will assign distinctive values to all three affected highlight groups, record the current number of ColorScheme autocmds, call `highlights.configure()`, and verify that the highlight definitions and autocmd count are unchanged. The test will restore the original highlight definitions afterward. The full repository test and lint suites will also run.

## Scope

This change does not introduce plugin-specific highlight groups, new configuration options, or changes to popup `winhighlight` mappings.
