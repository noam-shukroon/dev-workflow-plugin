---
name: qa
description: "Browser-based visual QA across viewports. Use when the user asks to test, QA, check visuals, verify layouts, or run browser tests across desktop/tablet/mobile."
---

# QA

Browser-based visual QA across viewports using `claude-in-chrome`.

## Phase 1 — Route Discovery

1. Read project structure to identify user-facing routes/pages.
2. If the app is running locally, use the dev server URL. If not, ask the user for the URL.
3. List all routes to test. Ask user to confirm or trim the list.

## Phase 2 — Viewport Testing (parallel agents)

For each route, test at three viewports:

| Viewport | Width | Height |
|----------|-------|--------|
| Desktop | 1440px | 900px |
| Tablet | 768px | 1024px |
| Mobile | 375px | 812px |

Use `superpowers: dispatching-parallel-agents` — one agent per route (each agent tests all 3 viewports for its route).

Per viewport, each agent checks:
- **Layout** — no horizontal overflow, elements properly aligned, responsive breakpoints working
- **Interactive elements** — buttons/links clickable, forms submittable, dropdowns opening correctly
- **Text** — no truncation, readable font sizes, correct text direction (RTL/LTR)
- **Z-index** — modals/drawers/tooltips layering correctly, no elements hidden behind others
- **Accessibility widget** — if present, opens and functions at all viewports

Each agent screenshots before and after interactions. Returns findings:
```
[R1-V1-F1] P1 — Button overlaps text on mobile
Route: /booking
Viewport: Mobile (375px)
Description: CTA button overlaps the service name when text wraps to 2 lines.
```

## Phase 3 — QA Report

Merge results into a structured report:

```
## QA Report

### /route-1 (2 findings)
[R1-V2-F1] P1 — Drawer slides wrong direction on tablet
[R1-V3-F1] P2 — FAB too close to screen edge on mobile

### /route-2 (0 findings)
All clear.

Summary: X findings across Y routes (Z P0, W P1, V P2)
```

Present report. Wait for user direction.

## Phase 4 — Fix & Re-verify

For each approved fix:
1. Apply the CSS/layout fix.
2. **Re-test in browser** at the affected viewport — screenshot to verify.
3. If fix doesn't resolve the issue after 2 attempts, mark as `needs-human` with reproduction steps.

## Phase 5 — Summary

```
QA complete:
- Routes tested: N
- Findings: X (Y fixed, Z deferred, W needs-human)
- Viewports: Desktop, Tablet, Mobile
```

Commit fixes if any were applied: `fix: QA fixes — [finding IDs]`
