# Remove Obsolete Resize Helpers Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Restore a clean Luacheck run by removing the unreachable legacy resize path.

**Architecture:** Keep the manager-based resize flow in `activate_ui()` unchanged. Delete only the obsolete helpers and their exclusive dependency.

**Tech Stack:** Lua, Neovim API, Plenary/Busted, Luacheck, StyLua, Selene

---

### Task 1: Remove the dead resize implementation

**Files:**
- Modify: `lua/spring-initializr/ui/init.lua`

- [ ] **Step 1: Verify RED**

Run `luacheck lua/ --globals vim` and confirm W211 reports `reopen_after_resize` as an unused recursive function.

- [ ] **Step 2: Remove obsolete code**

Delete the `events` import, `close_for_resize()`, and `reopen_after_resize()`. Do not alter `activate_ui()` or `autocmd_manager`.

- [ ] **Step 3: Verify GREEN**

Run Luacheck again and expect zero warnings/errors.

- [ ] **Step 4: Run repository verification**

Run the full Plenary test directory, targeted StyLua, Selene, and `git diff --check`; all must pass.

- [ ] **Step 5: Commit**

Commit the source cleanup as `refactor: remove obsolete resize helpers`.
