# Respect User Highlights Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Preserve user and colorscheme definitions for the global floating-window highlight groups used by Spring Initializr.

**Architecture:** Retain `highlights.configure()` as a compatibility boundary while removing all side effects from it. Verify behavior through the public function with real Neovim highlight and autocmd APIs.

**Tech Stack:** Lua, Neovim API, Plenary/Busted, StyLua, Luacheck, Selene

---

### Task 1: Preserve global highlights and ColorScheme hooks

**Files:**
- Create: `tests/styles/highlights_spec.lua`
- Modify: `lua/spring-initializr/styles/highlights.lua`

- [ ] **Step 1: Write the failing regression test**

Set custom `NormalFloat`, `FloatBorder`, and `NuiMenuSel` definitions, capture them with `nvim_get_hl`, and count current ColorScheme autocmds. Call `highlights.configure()` and assert the definitions and autocmd count remain identical.

- [ ] **Step 2: Run the focused test to verify RED**

Run:

```bash
nvim --headless -u tests/minimal_init.lua \
  -c "PlenaryBustedFile tests/styles/highlights_spec.lua" +q
```

Expected: FAIL because `configure()` currently replaces the custom definitions and registers an autocmd.

- [ ] **Step 3: Remove highlight side effects**

Remove the events dependency and the private highlight/autocmd helpers. Keep the public function as:

```lua
function M.configure() end
```

- [ ] **Step 4: Run the focused test to verify GREEN**

Run the focused Plenary command from Step 2.

Expected: 1 passing, 0 failures.

- [ ] **Step 5: Run repository verification**

Run:

```bash
nvim --headless -u tests/minimal_init.lua \
  -c "PlenaryBustedDirectory tests --exclude-pattern telescope_integration_spec" +q
stylua --check lua/spring-initializr/styles/highlights.lua tests/styles/highlights_spec.lua \
  --config-path=.stylua.toml
selene lua/
luacheck lua/ --globals vim
git diff --check
```

Expected: tests, targeted formatting, Selene, and diff hygiene pass. Luacheck may retain only the known unrelated `ui/init.lua:238` W211 warning from upstream `main`.

- [ ] **Step 6: Commit**

```bash
git add lua/spring-initializr/styles/highlights.lua tests/styles/highlights_spec.lua
git commit -m "fix: respect user highlight styles"
```
