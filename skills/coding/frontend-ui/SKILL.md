---
name: frontend-ui
description: Use when implementing, fixing, reviewing, or polishing frontend UI, including layout, responsive behavior, component states, visual consistency, and browser-rendered bugs.
---

# Frontend UI Skill

Use this skill for UI implementation, UI debugging, responsive layout fixes, and visual polish.

Do not use it for backend-only changes, non-rendering refactors, or purely data/model work.

## Workflow

1. Inspect the existing UI patterns before editing:
   - components already used for the same purpose
   - design system primitives
   - styling conventions
   - spacing, typography, and responsive breakpoints

2. Make the smallest scoped change that fits the existing system:
   - prefer existing components and utilities
   - prefer shadcn/ui, Tailwind, and local conventions when present
   - avoid new dependencies unless explicitly justified
   - avoid unrelated file churn

3. Build static structure before wiring dynamic behavior.

4. Preserve API contracts and existing user flows.

5. Add or preserve relevant states when applicable:
   - loading
   - empty
   - error
   - disabled
   - hover
   - focus-visible
   - active/selected

6. Use mobile-first responsive design:
   - avoid fixed widths on mobile
   - prevent horizontal overflow
   - use `min-w-0` in flex/grid text containers
   - wrap or scroll tables intentionally
   - constrain images with max width/height
   - check breakpoint transitions

7. Keep visual quality grounded in the surrounding product:
   - align spacing and type scale with nearby screens
   - keep hierarchy clear and scannable
   - ensure interactive elements look clickable
   - maintain readable contrast
   - avoid cramped touch targets on mobile

8. Extract reusable components only when repetition is clear or the project already has a matching abstraction.

9. Verify the rendered UI when possible:
   - run the relevant app or tests
   - inspect at mobile and desktop widths
   - check keyboard focus and disabled states
   - report any verification that could not be run

## Debugging

1. Reproduce or locate the root cause before editing.
2. Prefer targeted fixes over rewrites.
3. Check common causes:
   - horizontal overflow
   - missing `min-w-0`
   - fixed width on mobile
   - table overflow
   - unconstrained images
   - bad grid breakpoints
   - z-index or sticky conflicts
   - focus ring clipping
   - text truncation without accessible fallback
4. Do not rewrite the whole component unless explicitly requested or the current structure prevents a correct fix.
