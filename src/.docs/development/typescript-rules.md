# TypeScript Rules and Guidelines

## Type vs Interface Usage

### Use `type` for:

```typescript
// Union types
export type Status = "idle" | "loading" | "success" | "error";
export type Theme = "light" | "dark";

// Simple object shapes that won't be extended
export type Coordinates = {
	x: number;
	y: number;
};

// Function types
export type EventHandler<T> = (event: T) => void;
export type AsyncFunction<T> = () => Promise<T>;

// Computed types
export type NameCategory = (typeof NAME_CATEGORIES)[number];
export type LanguageKeys = keyof typeof LANGUAGES;
```

### Use `interface` for:

```typescript
// Objects that might be extended
export interface NameGeneratorConfig {
	defaultLanguage: string;
	maxNames: number;
}

export interface ExtendedConfig extends NameGeneratorConfig {
	enableHistory: boolean;
	customPatterns: Pattern[];
}

// Component props (can be extended)
export interface ButtonProps {
	variant?: "primary" | "secondary";
	disabled?: boolean;
	children: React.ReactNode;
}

export interface IconButtonProps extends ButtonProps {
	icon: string;
	iconPosition?: "left" | "right";
}
```

## Type Inference Guidelines

### Let TypeScript Infer When Possible

```typescript
// Good - TypeScript can infer these
const languages = ["aetherian", "draconic", "elvish"];
const [count, setCount] = useState(0);
const [isOpen, setIsOpen] = useState(false);

// TypeScript infers: string[]
const getLanguageNames = () => languages.map((lang) => lang.name);

// Bad - Unnecessary typing
const languages: string[] = ["aetherian", "draconic", "elvish"];
const [count, setCount] = useState<number>(0);
```

### Provide Types When Inference Isn't Clear

```typescript
// Good - Type needed for clarity
const [user, setUser] = useState<User | null>(null);
const [data, setData] = useState<LanguageData[]>([]);

// Generic functions need explicit types
const createArray = <T>(item: T, count: number): T[] => {
	return Array(count).fill(item);
};

// Complex return types
const processLanguageData = (raw: unknown): Result<FantasyLanguage> => {
	// Implementation
};
```

## Strict Type Definitions

### Required vs Optional Properties

```typescript
// Clear distinction between required and optional
export type NamePattern = {
	// Required properties
	pattern: string;
	weight: number;

	// Optional properties
	description?: string;
	tags?: string[];
	deprecated?: boolean;
};

// Use readonly for immutable data
export type ReadonlyConfig = {
	readonly defaultLanguage: string;
	readonly maxNames: number;
	readonly themes: readonly string[];
};
```

### Discriminated Unions

```typescript
// Good - Discriminated unions for different states
export type LoadingState =
	| { status: "idle" }
	| { status: "loading" }
	| { status: "success"; data: FantasyLanguage }
	| { status: "error"; error: string };

// Usage with type guards
const handleState = (state: LoadingState) => {
	switch (state.status) {
		case "idle":
			return <div>Ready to load</div>;
		case "loading":
			return <div>Loading...</div>;
		case "success":
			return <div>Data: {state.data.name}</div>; // TypeScript knows data exists
		case "error":
			return <div>Error: {state.error}</div>; // TypeScript knows error exists
	}
};
```

## Generic Types

### Generic Functions

```typescript
// Generic function with constraints
export const pickRandom = <T>(array: readonly T[]): T => {
	const index = Math.floor(Math.random() * array.length);
	return array[index];
};

// Generic with multiple type parameters
export const mapResult = <T, U, E = Error>(
	result: Result<T, E>,
	mapper: (value: T) => U
): Result<U, E> => {
	return result.success
		? { success: true, data: mapper(result.data) }
		: result;
};
```

### Generic Components

```typescript
// Generic component
export const List = <T extends { id: string }>({
	items,
	renderItem,
}: {
	items: T[];
	renderItem: (item: T) => React.ReactNode;
}) => {
	return (
		<div className="list">
			{items.map((item) => (
				<div key={item.id} className="list__item">
					{renderItem(item)}
				</div>
			))}
		</div>
	);
};

// Usage
<List items={languages} renderItem={(lang) => <span>{lang.name}</span>} />;
```

## Utility Types

### Common Utility Type Patterns

```typescript
// Pick specific properties
export type LanguageMetadata = Pick<
	FantasyLanguage,
	"id" | "name" | "description"
>;

// Make all properties optional
export type PartialConfig = Partial<NameGeneratorConfig>;

// Make specific properties required
export type RequiredLanguage = Required<Pick<FantasyLanguage, "id" | "name">> &
	Partial<FantasyLanguage>;

// Extract union type members
export type SuccessResult<T> = Extract<Result<T>, { success: true }>;

// Exclude union type members
export type ErrorResult<T> = Exclude<Result<T>, { success: true }>;
```

### Custom Utility Types

```typescript
// Create reusable utility types
export type NonEmptyArray<T> = [T, ...T[]];

export type AsyncReturnType<T extends (...args: any) => Promise<any>> =
	T extends (...args: any) => Promise<infer R> ? R : never;

// Deep readonly
export type DeepReadonly<T> = {
	readonly [P in keyof T]: T[P] extends object ? DeepReadonly<T[P]> : T[P];
};
```

## Type Guards and Assertions

### Type Guard Functions

```typescript
// Type guard for runtime checking
export const isFantasyLanguage = (data: unknown): data is FantasyLanguage => {
	return (
		typeof data === "object" &&
		data !== null &&
		"id" in data &&
		"name" in data &&
		"syllables" in data &&
		"patterns" in data
	);
};

// Generic type guard
export const isArrayOf = <T>(
	array: unknown,
	itemGuard: (item: unknown) => item is T
): array is T[] => {
	return Array.isArray(array) && array.every(itemGuard);
};

// Usage
if (isFantasyLanguage(data)) {
	// TypeScript now knows data is FantasyLanguage
	console.log(data.name);
}
```

### Safe Type Assertions

```typescript
// Use type guards instead of assertions when possible
const processData = (data: unknown) => {
	if (!isFantasyLanguage(data)) {
		throw new Error("Invalid language data");
	}

	// TypeScript knows data is FantasyLanguage
	return data.patterns;
};

// When assertions are necessary, be explicit
const element = document.getElementById("root") as HTMLElement;
const inputElement = event.target as HTMLInputElement;
```

## Error Handling Types

### Result Pattern

```typescript
// Result type for operations that can fail
export type Result<T, E = Error> =
	| { success: true; data: T }
	| { success: false; error: E };

// Helper functions
export const success = <T>(data: T): Result<T> => ({ success: true, data });
export const failure = <E>(error: E): Result<never, E> => ({
	success: false,
	error,
});

// Usage
const loadLanguage = async (id: string): Promise<Result<FantasyLanguage>> => {
	try {
		const data = await import(`@/assets/data/languages/${id}.json`);
		if (!isFantasyLanguage(data.default)) {
			return failure(new Error("Invalid language data"));
		}
		return success(data.default);
	} catch (error) {
		return failure(error as Error);
	}
};
```

### Error Types

```typescript
// Define specific error types
export class LanguageLoadError extends Error {
	constructor(public languageId: string, message: string) {
		super(message);
		this.name = "LanguageLoadError";
	}
}

export class ValidationError extends Error {
	constructor(public field: string, public value: unknown, message: string) {
		super(message);
		this.name = "ValidationError";
	}
}

// Union type for all app errors
export type AppError = LanguageLoadError | ValidationError | Error;
```

## React-Specific Types

### Component Props

```typescript
// Extend HTML attributes
export type ButtonProps = {
	variant?: "primary" | "secondary" | "danger";
	size?: "small" | "medium" | "large";
} & React.ButtonHTMLAttributes<HTMLButtonElement>;

// Children patterns
export type LayoutProps = {
	children: React.ReactNode;
	sidebar?: React.ReactNode;
};

// Event handlers
export type FormProps = {
	onSubmit: (data: FormData) => void;
	onFieldChange: (field: string, value: string) => void;
};
```

### Hook Types

```typescript
// Hook return types
export type UseNameGeneratorReturn = {
	names: string[];
	generateName: (language: string, category: string, seed?: string) => void;
	isLoading: boolean;
	error: string | null;
};

// Hook parameters
export type UseLocalStorageOptions<T> = {
	key: string;
	defaultValue: T;
	serializer?: {
		parse: (value: string) => T;
		stringify: (value: T) => string;
	};
};
```

## Module Declaration

### Ambient Types

```typescript
// types/global.d.ts
declare global {
	interface Window {
		__LANGUAGE_DATA_CACHE__?: Map<string, FantasyLanguage>;
	}
}

// Module augmentation
declare module "*.json" {
	const value: any;
	export default value;
}

// Third-party library types
declare module "seedrandom" {
	function seedrandom(seed?: string): () => number;
	export = seedrandom;
}
```

## Best Practices Summary

### DO:

-  Use type inference when TypeScript can determine the type
-  Prefer `type` for simple types, `interface` for extensible objects
-  Use discriminated unions for different states
-  Create type guards for runtime validation
-  Use Result pattern for error handling
-  Make types as specific as possible

### DON'T:

-  Over-annotate when TypeScript can infer
-  Use `any` type (use `unknown` instead)
-  Mix `interface` and `type` inconsistently
-  Rely on type assertions without validation
-  Create overly complex generic types
-  Ignore TypeScript errors with `@ts-ignore`

### Performance Considerations:

-  Use `const assertions` for immutable data
-  Avoid creating types in render functions
-  Use `readonly` for props that shouldn't change
-  Consider using `React.memo` with typed components
