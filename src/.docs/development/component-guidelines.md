# React Component Guidelines

## Component Categories

### 1. UI Components (`components/ui/`)

Generic, reusable components with no business logic.

```typescript
// Good - Generic and reusable
export const Button = ({
	children,
	variant = "primary",
	disabled = false,
	onClick,
	...props
}: ButtonProps) => {
	return (
		<button
			className={`button button--${variant}`}
			disabled={disabled}
			onClick={onClick}
			{...props}
		>
			{children}
		</button>
	);
};

// Bad - Too specific, contains business logic
export const GenerateNameButton = () => {
	const { generateName } = useNameGenerator();
	return (
		<button onClick={() => generateName("aetherian", "person_names")}>
			Generate
		</button>
	);
};
```

### 2. Feature Components (`components/`)

Business-specific components that use hooks and context.

```typescript
// Good - Feature-specific component
export const NameDisplay = () => {
	const { names, isLoading, error } = useNameGenerator();

	if (error) {
		return <div className="name-display name-display--error">{error}</div>;
	}

	return (
		<div className="name-display">
			{isLoading ? (
				<div className="name-display__loading">Generating names...</div>
			) : (
				<div className="name-display__results">
					{names.map((name, index) => (
						<div key={index} className="name-display__name">
							{name}
						</div>
					))}
				</div>
			)}
		</div>
	);
};
```

### 3. Page Components (`pages/`)

Top-level route components that compose features.

```typescript
// Good - Page component composition
export const Generator = () => {
	return (
		<div className="generator">
			<header className="generator__header">
				<h1>Fantasy Name Generator</h1>
			</header>

			<main className="generator__main">
				<LanguageSelector />
				<CategorySelector />
				<GenerationControls />
				<NameDisplay />
			</main>
		</div>
	);
};
```

## Component Structure Rules

### 1. Standard Component Template

```typescript
import React, { useState, useCallback, useEffect } from "react";
import type { ComponentNameProps } from "./types";
import "./ComponentName.scss";

export const ComponentName = ({
	prop1,
	prop2,
	onAction,
	children,
}: ComponentNameProps) => {
	// 1. State declarations
	const [localState, setLocalState] = useState<string>("");

	// 2. Context/hooks
	const { globalState, actions } = useCustomHook();

	// 3. Computed values
	const computedValue = useMemo(() => {
		return expensiveComputation(localState);
	}, [localState]);

	// 4. Event handlers
	const handleAction = useCallback(
		(value: string) => {
			setLocalState(value);
			onAction?.(value);
		},
		[onAction]
	);

	// 5. Effects
	useEffect(() => {
		// Side effects
		return () => {
			// Cleanup
		};
	}, []);

	// 6. Early returns/guards
	if (!prop1) {
		return (
			<div className="component-name component-name--empty">No data</div>
		);
	}

	// 7. Render
	return (
		<div className="component-name">
			<div className="component-name__content">{children}</div>
		</div>
	);
};
```

### 2. Props Interface Pattern

```typescript
// types.ts or inline
export type ComponentNameProps = {
	// Required props first
	id: string;
	title: string;

	// Optional props with defaults
	variant?: "primary" | "secondary";
	disabled?: boolean;

	// Event handlers
	onAction?: (value: string) => void;
	onChange?: (event: React.ChangeEvent<HTMLInputElement>) => void;

	// Children and content
	children?: React.ReactNode;

	// HTML attributes (when extending HTML elements)
} & React.ButtonHTMLAttributes<HTMLButtonElement>;
```

## Mobile-First Component Design

### 1. Mobile-First Layout

```typescript
export const ResponsiveGrid = ({ children }: { children: React.ReactNode }) => {
	return <div className="responsive-grid">{children}</div>;
};
```

```scss
.responsive-grid {
	// Mobile: Single column
	display: grid;
	gap: 1rem;
	padding: 1rem;

	// Tablet: Two columns
	@media (min-width: 768px) {
		grid-template-columns: repeat(2, 1fr);
		gap: 1.5rem;
		padding: 1.5rem;
	}

	// Desktop: Three columns
	@media (min-width: 1024px) {
		grid-template-columns: repeat(3, 1fr);
		gap: 2rem;
		padding: 2rem;
	}
}
```

### 2. Touch-Friendly Components

```typescript
export const TouchButton = ({ children, onClick, ...props }: ButtonProps) => {
	return (
		<button
			className="touch-button"
			onClick={onClick}
			// Minimum touch target size
			style={{ minHeight: "44px", minWidth: "44px" }}
			{...props}
		>
			{children}
		</button>
	);
};
```

## Performance Guidelines

### 1. Memoization

```typescript
// Memoize expensive components
export const ExpensiveList = memo(({ items }: { items: Item[] }) => {
	return (
		<div className="expensive-list">
			{items.map((item) => (
				<ExpensiveItem key={item.id} item={item} />
			))}
		</div>
	);
});

// Memoize with custom comparison
export const OptimizedComponent = memo(
	({ data, onAction }: Props) => {
		// Component implementation
	},
	(prevProps, nextProps) => {
		return prevProps.data.id === nextProps.data.id;
	}
);
```

### 2. Callback Optimization

```typescript
export const OptimizedParent = () => {
	const [count, setCount] = useState(0);
	const [name, setName] = useState("");

	// Stable reference prevents child re-renders
	const handleIncrement = useCallback(() => {
		setCount((prev) => prev + 1);
	}, []);

	// Dependencies only when needed
	const handleSubmit = useCallback(() => {
		submitData(name, count);
	}, [name, count]);

	return (
		<div>
			<ChildComponent onIncrement={handleIncrement} />
			<AnotherChild onSubmit={handleSubmit} />
		</div>
	);
};
```

## Error Handling in Components

### 1. Error Boundaries

```typescript
export const ComponentErrorBoundary = ({
	children,
	fallback,
}: {
	children: React.ReactNode;
	fallback?: React.ReactNode;
}) => {
	const [hasError, setHasError] = useState(false);

	useEffect(() => {
		const handleError = () => setHasError(true);
		window.addEventListener("error", handleError);
		return () => window.removeEventListener("error", handleError);
	}, []);

	if (hasError) {
		return (
			fallback || (
				<div className="error-boundary">
					<h3>Something went wrong</h3>
					<button onClick={() => setHasError(false)}>Try again</button>
				</div>
			)
		);
	}

	return <>{children}</>;
};
```

### 2. Error States in Components

```typescript
export const DataComponent = () => {
	const { data, isLoading, error } = useData();

	// Handle error state
	if (error) {
		return (
			<div className="data-component data-component--error">
				<p>Failed to load data: {error.message}</p>
				<button onClick={() => window.location.reload()}>Retry</button>
			</div>
		);
	}

	// Handle loading state
	if (isLoading) {
		return (
			<div className="data-component data-component--loading">
				<div className="spinner" />
				<p>Loading...</p>
			</div>
		);
	}

	// Handle empty state
	if (!data.length) {
		return (
			<div className="data-component data-component--empty">
				<p>No data available</p>
			</div>
		);
	}

	// Render data
	return (
		<div className="data-component">
			{data.map((item) => (
				<DataItem key={item.id} item={item} />
			))}
		</div>
	);
};
```

## Accessibility Guidelines

### 1. Semantic HTML

```typescript
export const AccessibleForm = () => {
	return (
		<form className="form">
			<fieldset className="form__section">
				<legend>Language Selection</legend>

				<label htmlFor="language-select" className="form__label">
					Choose Language:
				</label>
				<select id="language-select" className="form__select">
					<option value="aetherian">Aetherian</option>
					<option value="draconic">Draconic</option>
				</select>
			</fieldset>

			<button type="submit" className="form__submit">
				Generate Names
			</button>
		</form>
	);
};
```

### 2. ARIA Attributes

```typescript
export const AccessibleButton = ({
	children,
	isLoading,
	onClick,
}: ButtonProps) => {
	return (
		<button
			className="button"
			aria-busy={isLoading}
			aria-disabled={isLoading}
			disabled={isLoading}
			onClick={onClick}
		>
			{isLoading ? (
				<>
					<span className="button__spinner" aria-hidden="true" />
					<span className="sr-only">Loading...</span>
				</>
			) : (
				children
			)}
		</button>
	);
};
```

## Testing Considerations

### 1. Testable Components

```typescript
// Good - Easy to test
export const Counter = ({ initialValue = 0, onCountChange }: CounterProps) => {
	const [count, setCount] = useState(initialValue);

	const increment = () => {
		const newCount = count + 1;
		setCount(newCount);
		onCountChange?.(newCount);
	};

	return (
		<div className="counter">
			<span data-testid="count">{count}</span>
			<button data-testid="increment" onClick={increment}>
				+
			</button>
		</div>
	);
};

// Bad - Hard to test, too many dependencies
export const ComplexComponent = () => {
	const { state1 } = useContext(Context1);
	const { state2 } = useContext(Context2);
	const { data } = useFetch("/api/data");

	// Complex component logic...
};
```

## Component Documentation

### 1. JSDoc Comments

````typescript
/**
 * A reusable button component with multiple variants
 *
 * @example
 * ```tsx
 * <Button variant="primary" onClick={() => console.log('clicked')}>
 *   Click me
 * </Button>
 * ```
 */
export const Button = ({
	variant = "primary",
	children,
	onClick,
	...props
}: ButtonProps) => {
	return (
		<button
			className={`button button--${variant}`}
			onClick={onClick}
			{...props}
		>
			{children}
		</button>
	);
};
````

### 2. PropTypes (Optional)

```typescript
// If using PropTypes for runtime checking
import PropTypes from "prop-types";

Button.propTypes = {
	variant: PropTypes.oneOf(["primary", "secondary", "danger"]),
	children: PropTypes.node.isRequired,
	onClick: PropTypes.func,
	disabled: PropTypes.bool,
};
```
