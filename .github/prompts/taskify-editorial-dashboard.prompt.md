---
name: Taskify Editorial Dashboard
description: Redesign or extend the current Vue 3 Taskify app as an editorial, high-fashion minimalist task dashboard while preserving its existing task logic.
argument-hint: Optional: describe a preferred task workflow, visual asset, or dashboard detail.
agent: agent
---

Redesign the existing **Taskify** application as a polished editorial productivity dashboard. Inspect the current Vue entrypoint, components, styles, and any state-management store before editing. Keep the existing framework and preserve all task CRUD and state-management behavior; do not replace Pinia/Vuex if the repository already uses it, and do not add a state library when the app currently uses local Vue state.

## Visual direction

- Use a warm off-white canvas (`#EFECE6` or `#F4F1EA`), deep black (`#0D0D0D`), charcoal gray (`#666666`), and restrained muted earth accents.
- Use a condensed editorial display face such as Bebas Neue, Anton, or Impact for the TASKIFY wordmark, oversized uppercase headline, metrics, and card titles. Use a clean sans-serif such as DM Sans or Inter for controls and supporting copy.
- Build a centered TASKIFY topbar with task filters on the left and New Task, Analytics, and Account/Profile actions on the right. Keep the header clean, compact, and responsive.
- Add a restrained workspace/profile strip with user identity, productivity status, and a quick-add action.
- Lead with a full-width oversized headline such as **ELEVATE YOUR PRODUCTIVITY** or **ORGANIZE YOUR ESSENCE**, followed by concise Total Tasks, Due Today, and Completion Rate metrics.
- Present tasks in a responsive three- or four-column grid. Each rounded task card should have a muted abstract banner or relevant cover image, large uppercase title over the lower image area, concise description, priority, due date, and an accessible completion toggle.
- Keep the interface editorial and useful: avoid decorative marketing copy, excessive cards, generic gradients, and controls that only look interactive.

## Behavior

- Use Vue 3 Composition API and `<script setup>` in the existing component style.
- Preserve and support task creation, editing, deletion, completion/status changes, and existing Pinia/Vuex state behavior when present.
- Provide a minimal floating or glass-like create/edit task dialog with title, description, priority, and due date. Close it with the close button, backdrop, or Escape.
- Add useful task filters (All Tasks, In Progress, Completed, Priority), live search, and accurate Total Tasks, Due Today, and Completion Rate statistics.
- Use Vue `<TransitionGroup>` for card add/remove/filter movement and honor `prefers-reduced-motion`.
- Ensure controls work with keyboard and touch; use semantic elements, visible focus states, accessible names, and responsive layouts.

## Component boundaries

Prefer the repository's existing structure. When suitable, organize the feature as `App.vue` for state and CRUD, `Navbar.vue` for navigation/filters, `Dashboard.vue` for the hero, stats, and task grid, and `TaskCard.vue` for one task. Keep shared tokens and resets in the existing global stylesheet (for example `main.css`); follow the installed Tailwind version rather than adding an unnecessary config file.

After implementation, run the repository's build or closest focused validation. Report the changed files, what CRUD behavior remains available, and any verification that could not be completed.

{{{ input }}}
