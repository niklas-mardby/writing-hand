# System Architecture

## Overview

Fantasy Name Generator follows a modern React architecture with clear separation of concerns, using Context API for state management and a feature-based folder structure.

## Architecture Principles

### 1. Separation of Concerns

-  **Business Logic** (`lib/`) - Pure TypeScript, framework-agnostic
-  **React Components** (`components/`) - UI rendering and user interaction
-  **Custom Hooks** (`hooks/`) - React-specific reusable logic
-  **Context/State** (`context/`) - Global state management

### 2. Data Flow

```
User Interaction → Component → Hook → Context/Reducer → Business Logic → Data/API
                                ↓
User Interface  ← Component ← Hook ← Context/Reducer ← Business Logic ← Response
```

### 3. Dependency Direction

-  Components depend on hooks
-  Hooks depend on context
-  Context depends on business logic
-  Business logic is dependency-free

## Core Architecture Components

### State Management

```typescript
// Central state with Context API + useReducer
NameGeneratorContext
├── currentLanguage: string
├── availableLanguages: LanguageMetadata[]
├── generatedNames: GeneratedName[]
├── isLoading: boolean
├── error: string | null
└── settings: UserSettings
```

### Custom Hooks Layer

```typescript
// Hooks encapsulate complex logic and side effects
useNameGenerator()    // Main name generation logic
├── useLanguageLoader()   // Async language loading
├── useRandomSeed()       // Seedrandom management
├── useLocalStorage()     // Persistence
└── useNameHistory()      // Name history management
```

### Business Logic Layer

```typescript
// Pure functions in lib/ directory
nameGenerator/
├── generator.ts         // Core name generation
├── languageLoader.ts    // Language data loading
├── validators.ts        // Data validation
└── types.ts            // Type definitions

random/
├── seededRandom.ts     // Seedrandom wrapper
└── weightedSelection.ts // Weighted random selection
```

## Component Architecture

### Component Hierarchy

```
App
├── Router
│   ├── Home
│   ├── Generator
│   │   ├── LanguageSelector
│   │   ├── CategorySelector
│   │   ├── NameDisplay
│   │   └── GenerationControls
│   └── Languages
│       ├── LanguageList
│       └── LanguageDetail
└── ErrorBoundary
```

### Component Types

1. **Page Components** - Route-level components
2. **Feature Components** - Complex business logic components
3. **UI Components** - Reusable, generic components
4. **Layout Components** - Structure and positioning

## Data Architecture

### Language Data Structure

```
assets/data/
├── language-metadata.json     # Quick reference for all languages
└── languages/
    ├── aetherian.json        # Full language definition
    ├── draconic.json         # Full language definition
    └── elvish.json           # Full language definition
```

### Data Loading Strategy

1. **Eager**: Load metadata immediately
2. **Lazy**: Load full language data on demand
3. **Cache**: Keep loaded languages in memory
4. **Fallback**: Graceful degradation on load failure

## Routing Architecture

```typescript
// React Router with createBrowserRouter
const router = createBrowserRouter([
	{
		path: "/",
		element: <App />,
		errorElement: <ErrorPage />,
		children: [
			{ index: true, element: <Home /> },
			{ path: "generator", element: <Generator /> },
			{ path: "languages", element: <Languages /> },
			{ path: "languages/:id", element: <LanguageDetail /> },
		],
	},
]);
```

## Performance Architecture

### Code Splitting

```typescript
// Route-based splitting
const Generator = lazy(() => import("@/pages/Generator"));
const Languages = lazy(() => import("@/pages/Languages"));

// Component-based splitting for heavy components
const LanguageEditor = lazy(() => import("@/components/LanguageEditor"));
```

### Data Optimization

-  **Lazy loading** of language data
-  **Memoization** of expensive calculations
-  **Virtualization** for large lists (if needed)
-  **Caching** of loaded languages

### Bundle Optimization

-  **Tree shaking** via barrel exports
-  **Dynamic imports** for language data
-  **Asset optimization** through Vite
-  **Dead code elimination** with TypeScript

## Mobile-First Architecture

### Responsive Strategy

1. **Mobile First** - Design for smallest screen first
2. **Progressive Enhancement** - Add features for larger screens
3. **Touch Interactions** - Optimize for finger navigation
4. **Performance** - Minimize bundle size for mobile networks

### Breakpoint Strategy

```scss
// Mobile First breakpoints
$tablet: 768px;
$desktop: 1024px;
$wide: 1440px;

// Usage
.component {
	// Mobile styles (default)

	@media (min-width: $tablet) {
		// Tablet styles
	}

	@media (min-width: $desktop) {
		// Desktop styles
	}
}
```

## Error Handling Architecture

### Error Boundaries

-  **Global**: App-level error boundary
-  **Route**: Page-level error boundaries
-  **Feature**: Component-level error boundaries

### Error Types

```typescript
type AppError =
	| { type: "LANGUAGE_LOAD_ERROR"; languageId: string }
	| { type: "GENERATION_ERROR"; message: string }
	| { type: "VALIDATION_ERROR"; field: string }
	| { type: "NETWORK_ERROR"; details: string };
```

### Error Recovery

-  **Automatic retry** for transient errors
-  **Fallback UI** for critical failures
-  **User feedback** for recoverable errors
-  **Error reporting** for debugging

## Security Considerations

### Data Validation

-  **Input sanitization** for user-generated content
-  **Type validation** for loaded language data
-  **Schema validation** for JSON imports

### Safe Parsing

```typescript
// Safe JSON parsing with validation
const parseLanguageData = (data: unknown): Result<FantasyLanguage> => {
	if (!isValidLanguageData(data)) {
		return { success: false, error: new Error("Invalid language data") };
	}
	return { success: true, data: data as FantasyLanguage };
};
```

## Scalability Considerations

### Adding New Features

1. Create types in `lib/nameGenerator/types.ts`
2. Add business logic to appropriate `lib/` module
3. Create custom hook in `hooks/`
4. Build UI components in `components/`
5. Update context/reducer if needed

### Adding New Languages

1. Create JSON file in `assets/data/languages/`
2. Update `language-metadata.json`
3. Language automatically available in UI

### Performance Scaling

-  **Virtual scrolling** for large name lists
-  **Web Workers** for heavy computations
-  **Service Workers** for offline support
-  **CDN deployment** for language data
