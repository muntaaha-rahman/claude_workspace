---
name: pencil-workflow-conventions
description: Use when converting a documented idea (feature spec, project overview doc) into a visual mockup using Pencil (pen.dev) via its MCP tools. Covers how to approach the document first, match an existing design system, and work the canvas.
---

# Pencil (pen.dev) workflow conventions

**Status: not yet grounded in verified usage.** This is built from pen.dev's published documentation, not from watching the tools actually run in a connected session. Treat it as a starting point, and update it once `ui-designer` has actually used Pencil end to end — correct anything here that turns out wrong, and add what the docs didn't cover.

## Before opening Pencil

1. Read whatever describes the idea — a feature spec, the project's `CLAUDE.md`/`AGENTS.md`/`ARCHITECTURE.md`, or whatever the user points at. Understand what's being asked for before touching the canvas.
2. If the project has an existing design system doc (e.g. a `DESIGN_SYSTEM.md`), read it and match its colors, typography, and components — don't invent a new visual language for an existing project.

## In Pencil

1. Call `read_skill` first to learn Pencil's own agent-facing conventions before acting — it has its own internal skill system, separate from this one, that documents how to work its canvas.
2. Call `get_app_state`/`get_style` to understand the current document before changing it.
3. Use `execute` to actually draw/edit.
4. Work iteratively, not in one large silent batch — the user can watch the canvas update live, so check in at natural checkpoints rather than producing a finished mockup with no visibility into the steps.

## What this doesn't cover

Implementation code from the mockup (hand off to the coder) and requirements that weren't in the source doc (ask rather than guess at scope).
