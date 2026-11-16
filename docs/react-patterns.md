# React Patterns Reference

Composition and style guide for the front-end. The companion agent skill is
`.agents/skills/react-composition-patterns/` (local only).

Goals: **maintainability**, **scalability**, and **performance** (performance is
delegated to the [React Compiler](https://react.dev/learn/react-compiler), which
is enabled in `vite.config.ts`).

---

## Component API

### Always export a props interface

Every component exports an interface named `<ComponentName>Props`:

```tsx
export interface ButtonProps {
  variant: 'primary' | 'secondary';
  size: 'sm' | 'md' | 'lg';
  children: React.ReactNode;
}

export function Button({ variant, size, children }: ButtonProps) {
  // ...
}
```

- Strict TypeScript: no `any` (use `unknown` when truly unknown).
- Discriminated unions for variants.

---

## Composition patterns

### 1. Custom hooks — shared logic

Extract into a hook when logic is used by 2+ components, owns state/effects, or
should be tested in isolation.

### 2. Headless components — logic without UI

State + handlers live in a hook (`useAccordion`, `useTabs`); presentation stays
in the component. UI becomes swappable.

### 3. Compound components — shared context

Parent provides React context; children consume it. Children throw a
descriptive error when used outside their parent. Expose children as
`Select.Option` style properties.

---

## State management

| Kind of state    | Lives in                            |
| ---------------- | ----------------------------------- |
| Server data      | **React Query** (never copy elsewhere) |
| Global UI state  | **Zustand** (theme, sidebar, modals) |
| Form state       | **react-hook-form** + Zod           |
| Component-local  | **useState / useReducer** (default) |

Never place API data, form state, or component-local state in Zustand.

---

## Side effects

- **Never** fetch data inside `useEffect`; React Query owns data fetching.
- Required `useEffect` (subscriptions, timers, DOM sync):
  - Always return a cleanup function.
  - List all dependencies explicitly.
  - One effect per concern; extract extras into a hook.

---

## Performance

React Compiler auto-memoizes components and hooks. Do **not** write
`useMemo`, `useCallback`, or `React.memo` by hand — write idiomatic React.

Still be deliberate about:
- Virtualization for lists of 1000+ items.
- Web Workers for computations over ~16ms.
- Debouncing state that updates 60+ times per second.

---

## File organization

See [STRUCTURE.md](../STRUCTURE.md) (module-based). Dependencies flow
module → shared/core, never the reverse. Modules publish only their public API
via `index.ts`.

---

## Naming conventions

| Element           | Convention              | Example        |
| ----------------- | ----------------------- | -------------- |
| Components        | PascalCase              | `UserProfile`  |
| Hooks             | camelCase + `use`       | `useTournament`|
| Props interfaces  | PascalCase + `Props`    | `ButtonProps`  |
| Files/folders     | kebab-case              | `user-profile` |

---

## Review checklist

- [ ] Props interface exported (`<Name>Props`)
- [ ] Remote data through React Query
- [ ] Local state by default; Zustand only for global UI
- [ ] Reusable logic extracted into a hook
- [ ] No manual memoization
- [ ] No `any`
- [ ] Imports through module barrels
