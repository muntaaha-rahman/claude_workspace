---
name: accessibility-conventions
description: Use when reviewing or testing any UI/frontend code — components, pages, forms — for accessibility (WCAG-style) issues. Loaded by code-reviewer for a static read-through and by qa-tester for an automated/runtime check. Does not apply to pure backend logic with no rendered markup.
---

# Accessibility conventions

No project currently has a11y tooling installed (no `axe-core`, `jest-axe`, or `eslint-plugin-jsx-a11y` found anywhere in the repos as of this writing) — treat every check here as something to do manually until that changes.

## Static checks (for code-reviewer — read the markup/JSX)

- **Interactive elements use real semantic tags.** `<button>`/`<a>` for clickable things, not `<div onClick>` or `<span onClick>` with no keyboard handler. If a non-native element must be interactive, it needs `role`, `tabIndex`, and both click and keyboard (`onKeyDown`) handlers.
- **Images have meaningful `alt`.** Decorative images get `alt=""`, not a missing attribute. Icon-only buttons need an `aria-label` describing the action, not just a visual icon.
- **Form inputs have an associated label** (`<label htmlFor>` or `aria-label`), not just adjacent placeholder text.
- **Color isn't the only signal.** If an error/success state is conveyed by color alone (e.g. a red border), there should also be text or an icon — check this especially against this team's dark UI (`#0B0A0E` backgrounds, `#D1F801`/`#C8E63A` accents) where contrast is easy to get wrong.
- **Focus order follows visual/DOM order.** Watch for `tabIndex` values that reorder focus unexpectedly, and for modals/dialogs that don't trap focus while open.
- **Headings are hierarchical**, not skipped or chosen for visual size (don't use an `<h4>` just because it "looks right" when it should be an `<h2>`).

## Runtime checks (for qa-tester — actually verify, don't just read)

- If the project has no a11y testing tool yet, say so and recommend adding one (`@axe-core/playwright` if the project already uses Playwright, `jest-axe` if it uses Jest/RTL) rather than silently skipping automated verification — but don't install tooling as a side effect of an unrelated task without flagging it first.
- Where a tool exists, run it against the rendered page/component and report violations with their severity, not just a pass/fail count.
- Keyboard-only navigation: confirm every interactive element reachable by `Tab` and operable without a mouse, especially modals, dropdowns, and custom components built from `div`/`span`.

## What this skill is not for

Pure backend logic, API responses, or anything with no rendered markup — there's no accessibility angle to check there. Load this only when the code under review/test actually renders UI.
