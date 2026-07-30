# Dependency Selection Hint Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add an actionable empty-state hint to the Selected Dependencies panel and remove it automatically once dependencies are selected.

**Architecture:** Exercise the existing public `update_display()` boundary with a real Neovim buffer. Make the smallest production change inside `render_dependency_lines()` so the populated card-rendering path remains untouched.

**Tech Stack:** Lua, Neovim API, Plenary/Busted, StyLua, Luacheck, Selene

---

### Task 1: Cover empty and populated dependency rendering

**Files:**
- Create: `tests/ui/dependencies_display_spec.lua`
- Modify: `lua/spring-initializr/ui/components/dependencies/dependencies_display.lua`

- [ ] **Step 1: Write the failing empty-state test**

Create a real scratch buffer, assign it to `dependencies_display.state.dependencies_panel`, clear `picker.selected_dependencies_full`, call `update_display()`, and assert the buffer contains:

```lua
{
    "No dependencies selected",
    "Press <CR> on Add Dependencies to select dependencies.",
}
```

- [ ] **Step 2: Run the focused test to verify RED**

Run:

```bash
nvim --headless -u tests/minimal_init.lua \
  -c "PlenaryBustedFile tests/ui/dependencies_display_spec.lua" +q
```

Expected: FAIL because the buffer currently contains only `No dependencies selected`.

- [ ] **Step 3: Implement the minimal empty-state hint**

Change the empty branch of `render_dependency_lines()` to:

```lua
return {
    "No dependencies selected",
    "Press <CR> on Add Dependencies to select dependencies.",
}
```

- [ ] **Step 4: Add populated-state regression coverage**

Set `picker.selected_dependencies_full` to one dependency, call `update_display()`, and assert no rendered line contains the hint text. This proves the hint disappears when selections exist.

- [ ] **Step 5: Run the focused test to verify GREEN**

Run the focused Plenary command from Step 2.

Expected: 2 passing, 0 failures.

- [ ] **Step 6: Run repository verification**

Run:

```bash
nvim --headless -u tests/minimal_init.lua \
  -c "PlenaryBustedDirectory tests --exclude-pattern telescope_integration_spec" +q
stylua --check lua/ tests/ --config-path=.stylua.toml
luacheck lua/ --globals vim
selene lua/
git diff --check
```

Expected: tests and formatting pass. If the known unrelated `ui/init.lua` recursive-function Luacheck warning remains on upstream `main`, document it precisely rather than changing unrelated code.

- [ ] **Step 7: Commit**

```bash
git add docs/superpowers/specs/2026-07-31-dependency-selection-hint-design.md \
  docs/superpowers/plans/2026-07-31-dependency-selection-hint.md \
  tests/ui/dependencies_display_spec.lua \
  lua/spring-initializr/ui/components/dependencies/dependencies_display.lua
git commit -m "feat: guide empty dependency selection"
```
