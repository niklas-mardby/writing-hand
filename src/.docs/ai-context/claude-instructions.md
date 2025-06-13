# Claude Instructions for Fantasy Name Generator

## Project Overview

React + TypeScript fantasy name generator using seedrandom for deterministic random name generation. Users can generate names for fantasy worlds with consistent linguistic patterns.

## Tech Stack

-  **Frontend**: React 18+ with TypeScript
-  **Build Tool**: Vite
-  **Styling**: SCSS with BEM methodology (Mobile First)
-  **Routing**: React Router with createBrowserRouter
-  **State**: Context API + useReducer
-  **Random**: seedrandom library (NOT Math.random)
-  **UI Library**: Custom components only
-  **Package Manager**: npm

## Core Architecture Rules

### React Patterns

-  Modern React with hooks only (no class components)
-  Custom hooks for reusable logic
-  Context API + reducer for global state management
-  React Router with createBrowserRouter
-  Declarative patterns - avoid direct DOM manipulation
-  Use React's event system, not DOM events

### Code Quality Standards

-  **Clean Code**: DRY, clear intentions, readable code
-  **TypeScript**: Use type inference, avoid unnecessary typing
-  **Types vs Interfaces**: Use `type` for simple types, `interface` only for extensible objects
-  **Performance**: Optimize for good performance
-  **Mobile First**: All designs start with mobile, then scale up

### File Organization

-  **Feature-based** folder structure (not type-based)
-  **Co-location**: Components with their SCSS files
-  **Barrel exports**: Use index.ts files for clean imports
-  **Separation of concerns**: Business logic in lib/, React in components/

### SCSS & Styling

-  **Mobile First** responsive design
-  **BEM methodology** for CSS classes
-  **@use rule** instead of deprecated @import
-  **SCSS variables** and mixins in abstracts/
-  **Component-scoped** styles

### Data Management

-  Large language JSON files in `src/assets/data/`
-  Lazy loading with dynamic imports
-  Metadata separate from language data
-  Seedrandom for reproducible results

## Key Constraints

-  NO direct DOM manipulation
-  NO Math.random() - use seedrandom
-  NO class components
-  NO @import in SCSS - use @use
-  NO unnecessary TypeScript typing
-  NO third-party UI libraries

## Development Workflow

1. Read requirements in `/project/requirements.md`
2. Follow coding standards in `/development/coding-standards.md`
3. Use patterns from `/ai-context/common-patterns.md`
4. Implement mobile-first responsive design
5. Test on mobile viewport first

## Important Files to Reference

-  `coding-standards.md` - Detailed code style rules
-  `architecture.md` - System design decisions
-  `common-patterns.md` - Implementation templates
-  `data-structure.md` - Language data format

When implementing features, always start with mobile design and scale up to desktop.
