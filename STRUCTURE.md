# Structure

Folder and file structure of the front-end. Organization is **module-based**:
each functional module lives self-contained under `src/modules/` and is created
when the feature requires it (no empty modules are pre-created).

## General structure

```
src/
├── modules/          # Functional modules (auth, tournament, ...)
│   └── <module>/     # Each module is created when a feature requires it
│       ├── components/   # Module-specific components
│       ├── hooks/        # Module hooks
│       ├── services/     # API calls (REST / socket.io)
│       ├── schemas/      # Zod validations
│       ├── types/        # Module-local types
│       ├── utils/        # Module-local utilities
│       └── index.ts      # Module public API
│
├── shared/           # Reusable code across modules
│   ├── components/   # Generic UI (Button, Table, Modal, ...)
│   ├── hooks/        # Cross-cutting hooks (useDebounce, useMediaQuery, ...)
│   ├── schemas/      # Shared Zod schemas
│   ├── constants/    # Domain catalog (categories, modalities, ...)
│   ├── utils/        # Pure helper functions
│   └── types/        # Types shared across modules
│
├── core/             # Global infrastructure (not touched per module)
│   ├── config/       # API client, environment variables
│   ├── providers/    # React Query, i18n, theme, etc.
│   ├── stores/       # Global state (Zustand)
│   ├── router/       # React Router configuration
│   └── guards/       # Auth guards, role guards
│
├── layouts/          # Page shells (auth, dashboard, public, referee)
├── types/            # Global types (User, Session, ...)
│
├── App.tsx
├── main.tsx
└── index.css
```

## Naming conventions

| Element       | Convention                    | Example                |
| ------------- | ----------------------------- | ---------------------- |
| Folders       | `kebab-case`                  | `group-phase/`         |
| Files         | `kebab-case`                  | `user-profile.tsx`     |
| Components    | `PascalCase`                  | `UserProfile.tsx`      |
| Hooks         | `camelCase` with `use` prefix | `useTournament.ts`     |
| Types/Schemas | `PascalCase`                  | `ParticipantSchema.ts` |
| Services      | `camelCase`                   | `tournamentService.ts` |

## Placement rules

- **Inside a module** → used only by that module.
- **Inside `shared/`** → used by two or more modules.
- **Inside `core/`** → cross-cutting and not domain-related
  (routing, providers, configuration).
- **Inside `layouts/`** → page shells wrapping routes.

Code may not move from `shared/` or `core/` into importing from a module:
dependencies always flow from the module toward `shared/`/`core/`, never the
other way around.

Each module exposes **only** what is needed through its `index.ts`:

```ts
// modules/<module>/index.ts
export { TournamentList } from "./components/tournament-list";
export { useTournaments } from "./hooks/use-tournaments";
export type { Tournament } from "./types/tournament";
```

No other module may import directly from internal paths
(e.g. `modules/tournament/components/...`); always through the barrel export.
