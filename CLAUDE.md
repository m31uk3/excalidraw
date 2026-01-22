# CLAUDE.md

This document provides a comprehensive guide to the Excalidraw codebase for AI assistants.

## Project Overview

Excalidraw is a free, open-source virtual whiteboard with a hand-drawn style. The project is organized as a **Yarn workspace monorepo** with clear separation between the core library (published to npm) and the full-featured web application.

## Repository Structure

```
excalidraw/
├── packages/                    # Core npm packages
│   ├── excalidraw/             # Main React component (@excalidraw/excalidraw)
│   ├── common/                 # Shared utilities & types (@excalidraw/common)
│   ├── element/                # Element manipulation (@excalidraw/element)
│   ├── math/                   # 2D geometry utilities (@excalidraw/math)
│   └── utils/                  # Export & rendering utils (@excalidraw/utils)
├── excalidraw-app/             # Web application (excalidraw.com)
├── examples/                   # Integration examples
│   ├── with-nextjs/
│   └── with-script-in-browser/
├── dev-docs/                   # Documentation site (Docusaurus)
├── public/                     # Static assets
└── scripts/                    # Build and release scripts
```

## Package Details

### @excalidraw/excalidraw
Main React component library for embedding Excalidraw:
- `components/` - UI components (App, MainMenu, ColorPicker, etc.)
- `actions/` - Action handlers (duplicate, align, export, etc.)
- `renderer/` - Canvas rendering logic
- `scene/` - Scene management and element storage
- `fonts/` - Font families (Virgil, Cascadia, Nunito, etc.)
- `css/` - SCSS stylesheets

### @excalidraw/common
Shared utilities across packages:
- Constants and type definitions
- Color utilities (tinycolor2 integration)
- Editor interface definitions
- Key bindings and keyboard shortcuts

### @excalidraw/element
Element creation and manipulation:
- Element types and properties
- Binding logic for arrows/connections
- Collision detection
- Frame and group management
- Flowchart/elbow arrow generation

### @excalidraw/math
2D geometric mathematics:
- Vector operations
- Angle, curve, ellipse, line calculations
- Polygon and rectangle utilities
- Point and segment operations

### @excalidraw/utils
Export and utility functions:
- PNG/SVG export
- PNG chunk manipulation
- RoughJS integration
- File system access helpers

### excalidraw-app
Full web application with additional features:
- Real-time collaboration (Socket.io)
- End-to-end encryption
- Firebase integration
- PWA support
- Local-first auto-save

## Development Commands

### Essential Commands

```bash
yarn                     # Install dependencies
yarn start               # Start dev server (port 3001)
yarn test:update         # Run tests with snapshot updates (REQUIRED before commits)
yarn test:typecheck      # TypeScript type checking
yarn fix                 # Auto-fix linting and formatting
```

### Testing Commands

```bash
yarn test:app            # Run Vitest in watch mode
yarn test:update         # Update test snapshots
yarn test:coverage       # Generate coverage reports
yarn test:ui             # Open Vitest UI dashboard
```

### Build Commands

```bash
yarn build:app           # Build web application
yarn build:packages      # Build all npm packages
yarn build:excalidraw    # Build main excalidraw package
yarn build:common        # Build common package
yarn build:element       # Build element package
yarn build:math          # Build math package
```

### Code Quality Commands

```bash
yarn test:typecheck      # TypeScript validation
yarn test:code           # ESLint validation
yarn test:other          # Prettier formatting check
yarn fix                 # Auto-fix all issues
yarn fix:code            # Fix linting only
yarn fix:other           # Fix formatting only
```

## Development Workflow

1. **Package Development**: Work in `packages/*` for core editor features
2. **App Development**: Work in `excalidraw-app/` for app-specific features
3. **Testing**: Always run `yarn test:update` before committing
4. **Type Safety**: Use `yarn test:typecheck` to verify TypeScript
5. **Code Quality**: Run `yarn fix` to auto-fix linting/formatting issues

## Architecture Notes

### Build System

- **Yarn workspaces** for monorepo management
- **Vite** for web application bundling
- **esbuild** for package building
- **TypeScript** throughout with strict configuration

### Package Aliases

Internal packages use path aliases (configured in `vitest.config.mts` and `packages/tsconfig.base.json`):

```
@excalidraw/common   → packages/common/src/index.ts
@excalidraw/element  → packages/element/src/index.ts
@excalidraw/excalidraw → packages/excalidraw/index.tsx
@excalidraw/math     → packages/math/src/index.ts
@excalidraw/utils    → packages/utils/src/index.ts
```

### State Management

- **Jotai** for atom-based state management
- App-specific Jotai stores in `excalidraw-app/`:
  - `app-jotai.ts` - App-level state atoms
  - `editor-jotai.ts` - Editor state atoms
- Jotai imports are restricted to app-specific modules (enforced by ESLint)

### Component Architecture

- React 19 with functional components and hooks
- Context providers for shared state
- Components co-located in `components/` directories
- SCSS stylesheets parallel to components

## Testing

### Framework

- **Vitest** with jsdom environment
- Canvas mocking via `vitest-canvas-mock`
- Global test utilities enabled

### Test Organization

- Unit tests co-located with source files (`.test.ts`, `.test.tsx`)
- Integration tests in `tests/` directories
- Snapshot testing for complex operations
- Test helpers in `packages/excalidraw/tests/helpers/`

### Coverage Thresholds

- Lines: 60%
- Branches: 70%
- Functions: 63%
- Statements: 60%

## Code Quality Standards

### TypeScript

- Strict mode enabled
- Type-only imports enforced (`import type { ... }`)
- No implicit any
- Declaration generation for packages

### ESLint

- Strict import ordering (external before internal @excalidraw)
- Max warnings: 0 (strict mode)
- React best practices enforced

### Prettier

- Shared config via `@excalidraw/prettier-config`
- Applied to CSS, SCSS, JSON, Markdown, HTML, YAML

### Pre-commit Hooks

- Husky runs lint-staged on commit
- Auto-fixes linting and formatting issues
- Runs on modified TypeScript/JavaScript files

## Key Conventions

### File Organization

```
component/
├── ComponentName.tsx      # Main component
├── ComponentName.test.tsx # Co-located tests
├── ComponentName.scss     # Component styles
└── index.ts              # Public exports
```

### Import Order

1. External dependencies (react, etc.)
2. Internal @excalidraw packages
3. Relative imports

### Styling

- SCSS with CSS variables for theming
- Dark mode support via CSS variables
- Font files with preloading
- Component-scoped styles

### Data Flow

- Unidirectional state management
- Element store managed in `@excalidraw/element`
- Delta-based changes for collaboration
- Scene graph structure for rendering

## Environment Configuration

### Development (`.env.development`)

- Dev server: `http://localhost:3001`
- WebSocket server: `http://localhost:3002`
- PWA disabled
- Tracking disabled

### Production (`.env.production`)

- WebSocket: `https://oss-collab.excalidraw.com`
- AI backend: `https://oss-ai.excalidraw.com`
- Firebase production config

## Key Dependencies

### Runtime

- React 19.0.0
- Jotai 2.11.0 (state management)
- Socket.io-client 4.7.2 (collaboration)
- Firebase 11.3.1 (backend services)
- RoughJS 4.6.4 (hand-drawn rendering)

### Development

- Vitest 3.0.6
- Vite 5.0.12
- esbuild 0.19.10
- TypeScript 5.9.3

## Common Tasks

### Adding a New Action

1. Create action file in `packages/excalidraw/actions/`
2. Register action in `actions/index.ts`
3. Add keyboard shortcut in `@excalidraw/common` if needed
4. Write tests in `*.test.ts` file

### Adding a New Component

1. Create component in `packages/excalidraw/components/`
2. Add SCSS styles alongside component
3. Export from component's `index.ts`
4. Write co-located tests

### Adding a New Element Type

1. Define type in `@excalidraw/element`
2. Add rendering logic in `renderer/`
3. Add creation/manipulation in element package
4. Update tests and snapshots

## CI/CD Workflows

- **test.yml**: Runs tests on push to master
- **lint.yml**: Runs linting on pull requests
- **test-coverage-pr.yml**: Coverage reporting
- **size-limit.yml**: Bundle size monitoring
- **autorelease-excalidraw.yml**: Automated releases

## Resources

- **Repository**: https://github.com/excalidraw/excalidraw
- **Documentation**: https://docs.excalidraw.com
- **Web App**: https://excalidraw.com
- **License**: MIT
