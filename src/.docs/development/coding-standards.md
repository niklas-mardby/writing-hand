# Coding Standards

## File Naming Conventions

### Components

-  **PascalCase** for component files: `Button.tsx`, `NameDisplay.tsx`
-  **PascalCase** for component directories: `Button/`, `LanguageSelector/`
-  **camelCase** for SCSS files: `button.scss`, `nameDisplay.scss`

### Hooks

-  **camelCase** starting with 'use': `useNameGenerator.ts`, `useRandomSeed.ts`

### Utilities & Libraries

-  **camelCase**: `seededRandom.ts`, `languageLoader.ts`, `validators.ts`

### Types

-  **camelCase** with .ts extension: `nameGenerator.ts`, `global.d.ts`

### Constants

-  **SCREAMING_SNAKE_CASE** for constants: `DEFAULT_LANGUAGE`, `MAX_NAME_LENGTH`

## Import Order

```typescript
// 1. React imports
import React, { useState, useCallback, useContext } from "react";

// 2. Third-party libraries
import seedrandom from "seedrandom";
import { v4 as uuidv4 } from "uuid";

// 3. Internal components/hooks
import { Button, Input } from "@/components/ui";
import { useNameGenerator } from "@/hooks";

// 4. Types
import type { FantasyLanguage, NamePattern } from "@/lib/nameGenerator/types";

// 5. Relative imports
import "./ComponentName.scss";
```

## Component Structure Template

```typescript
import React, { useState, useCallback } from "react";
import type { ComponentProps } from "./types";
import "./ComponentName.scss";

export const ComponentName = ({ prop1, prop2 }: ComponentProps) => {
	// 1. State and hooks first
	const [localState, setLocalState] = useState("");
	const { globalState, actions } = useCustomHook();

	// 2. Event handlers
	const handleClick = useCallback(() => {
		// Handler logic
	}, []);

	// 3. Effects (if needed)
	useEffect(() => {
		// Effect logic
	}, []);

	// 4. Early returns/guards
	if (!prop1) return null;

	// 5. Render logic
	return (
		<div className="component-name">
			<div className="component-name__content">{/* JSX content */}</div>
		</div>
	);
};
```

## Variable Naming

```typescript
// Use descriptive names
const generatePersonName = () => {
	/* ... */
};
const currentLanguageId = "aetherian";
const selectedNamePatterns = [];

// Boolean variables with is/has/can/should prefix
const isLoading = false;
const hasError = true;
const canGenerate = true;
const shouldUpdate = false;

// Event handlers with handle prefix
const handleSubmit = () => {
	/* ... */
};
const handleLanguageChange = () => {
	/* ... */
};
```

## Function Definitions

```typescript
// Prefer arrow functions for consistency
const generateName = (language: string, category: string) => {
	// Implementation
};

// Use function declarations for hoisted functions if needed
function validateLanguageData(data: unknown): data is FantasyLanguage {
	// Validation logic
}
```

## TypeScript Usage

### Type vs Interface

```typescript
// Use 'type' for simple types
export type NameCategory = "person_names" | "place_names";
export type LoadingState = "idle" | "loading" | "success" | "error";

// Use 'interface' only for extensible objects
export interface NameGeneratorConfig {
	defaultLanguage: string;
	maxNames: number;
}
```

### Type Inference

```typescript
// Good - let TypeScript infer
const languages = ["aetherian", "draconic", "elvish"];
const [count, setCount] = useState(0);

// Avoid unnecessary typing
const onClick = (event: React.MouseEvent) => {
	/* ... */
};

// Type when inference isn't clear
const [user, setUser] = useState<User | null>(null);
```

## Error Handling

```typescript
// Use Result pattern for operations that can fail
type Result<T, E = Error> =
	| { success: true; data: T }
	| { success: false; error: E };

const loadLanguage = async (id: string): Promise<Result<FantasyLanguage>> => {
	try {
		const data = await import(`@/assets/data/languages/${id}.json`);
		return { success: true, data: data.default };
	} catch (error) {
		return { success: false, error: error as Error };
	}
};
```

## Performance Guidelines

```typescript
// Use useCallback for event handlers
const handleClick = useCallback(() => {
	// Handler logic
}, [dependency]);

// Use useMemo for expensive calculations
const expensiveValue = useMemo(() => {
	return computeExpensiveValue(data);
}, [data]);

// Lazy load components and data
const LazyComponent = lazy(() => import("./HeavyComponent"));
const languageData = await import(`@/assets/data/languages/${id}.json`);
```

## Comments and Documentation

```typescript
/**
 * Generates a fantasy name using syllable patterns
 * @param language - The language configuration to use
 * @param category - Type of name to generate (person_names, place_names, etc.)
 * @param seed - Optional seed for reproducible results
 * @returns Generated name string
 */
export const generateName = (
	language: FantasyLanguage,
	category: string,
	seed?: string
): string => {
	// Implementation details...
};

// Use inline comments for complex logic
const selectPattern = (patterns: NamePattern[]) => {
	// Calculate weighted selection based on pattern weights
	const totalWeight = patterns.reduce((sum, p) => sum + p.weight, 0);
	// ... rest of logic
};
```

## Constants and Configuration

```typescript
// constants.ts
export const DEFAULT_LANGUAGE = "aetherian";
export const MAX_GENERATED_NAMES = 50;
export const LANGUAGE_CACHE_TTL = 5 * 60 * 1000; // 5 minutes

// Use const assertions for immutable data
export const NAME_CATEGORIES = [
	"person_names",
	"place_names",
	"artifact_names",
] as const;

export type NameCategory = (typeof NAME_CATEGORIES)[number];
```

## Testing Patterns (when tests are added)

```typescript
// Component testing
describe("NameDisplay", () => {
	it("should display generated name", () => {
		// Test implementation
	});
});

// Hook testing
describe("useNameGenerator", () => {
	it("should generate names with seed", () => {
		// Test implementation
	});
});
```
