# Common Implementation Patterns

## Custom Hook Pattern

```typescript
export const useNameGenerator = () => {
	const { state, dispatch } = useContext(NameGeneratorContext);

	const generateName = useCallback(
		(languageId: string, category: string, seed?: string) => {
			// Implementation using seedrandom
			dispatch({
				type: "GENERATE_NAME",
				payload: { languageId, category, seed },
			});
		},
		[dispatch]
	);

	return {
		generateName,
		names: state.names,
		currentLanguage: state.currentLanguage,
		isLoading: state.isLoading,
	};
};
```

## Component with Context Pattern

```typescript
export const NameDisplay = () => {
	const { names, generateName, isLoading } = useNameGenerator();

	const handleGenerate = useCallback(() => {
		generateName("aetherian", "person_names");
	}, [generateName]);

	return (
		<div className="name-display">
			{isLoading ? (
				<div className="name-display__loading">Generating...</div>
			) : (
				<div className="name-display__result">{names[0]}</div>
			)}
			<button
				className="name-display__button"
				onClick={handleGenerate}
				disabled={isLoading}
			>
				Generate Name
			</button>
		</div>
	);
};
```

## Async Data Loading Pattern

```typescript
// lib/nameGenerator/languageLoader.ts
export const loadLanguage = async (id: string): Promise<FantasyLanguage> => {
	try {
		const module = await import(`@/assets/data/languages/${id}.json`);
		return module.default;
	} catch (error) {
		throw new Error(`Failed to load language: ${id}`);
	}
};

// In component or hook
const [language, setLanguage] = useState<FantasyLanguage | null>(null);
const [loading, setLoading] = useState(false);

const loadLanguageData = useCallback(async (id: string) => {
	setLoading(true);
	try {
		const data = await loadLanguage(id);
		setLanguage(data);
	} catch (error) {
		console.error("Failed to load language:", error);
	} finally {
		setLoading(false);
	}
}, []);
```

## Context + Reducer Pattern

```typescript
// context/nameGeneratorReducer.ts
type NameGeneratorAction =
	| { type: "SET_LANGUAGE"; payload: string }
	| {
			type: "GENERATE_NAME";
			payload: { languageId: string; category: string; seed?: string };
	  }
	| { type: "SET_LOADING"; payload: boolean };

export const nameGeneratorReducer = (
	state: NameGeneratorState,
	action: NameGeneratorAction
): NameGeneratorState => {
	switch (action.type) {
		case "SET_LANGUAGE":
			return { ...state, currentLanguage: action.payload };
		case "GENERATE_NAME":
			// Name generation logic
			return { ...state, names: [...state.names, generatedName] };
		case "SET_LOADING":
			return { ...state, isLoading: action.payload };
		default:
			return state;
	}
};
```

## Mobile-First Responsive Component

```typescript
export const LanguageSelector = () => {
	const { currentLanguage, setLanguage } = useNameGenerator();

	return (
		<div className="language-selector">
			<select
				className="language-selector__select"
				value={currentLanguage}
				onChange={(e) => setLanguage(e.target.value)}
			>
				<option value="aetherian">Aetherian</option>
				<option value="draconic">Draconic</option>
			</select>
		</div>
	);
};
```

```scss
// Mobile First SCSS
.language-selector {
	&__select {
		width: 100%;
		padding: 1rem;
		font-size: 1.125rem;
		border: 2px solid var(--color-border);
		border-radius: 0.5rem;

		// Tablet
		@media (min-width: 768px) {
			width: auto;
			min-width: 200px;
		}

		// Desktop
		@media (min-width: 1024px) {
			font-size: 1rem;
		}
	}
}
```

## Error Boundary Pattern

```typescript
export const ErrorBoundary = ({ children }: { children: React.ReactNode }) => {
	const [hasError, setHasError] = useState(false);

	useEffect(() => {
		const handleError = () => setHasError(true);
		window.addEventListener("error", handleError);
		return () => window.removeEventListener("error", handleError);
	}, []);

	if (hasError) {
		return (
			<div className="error-boundary">
				<h2>Something went wrong</h2>
				<button onClick={() => setHasError(false)}>Try again</button>
			</div>
		);
	}

	return <>{children}</>;
};
```

## Seedrandom Usage Pattern

```typescript
// lib/random/seededRandom.ts
import seedrandom from "seedrandom";

export const createSeededRandom = (seed?: string) => {
	const rng = seedrandom(seed);

	return {
		random: () => rng(),
		randomInt: (min: number, max: number) =>
			Math.floor(rng() * (max - min + 1)) + min,
		randomChoice: <T>(array: T[]): T =>
			array[Math.floor(rng() * array.length)],
	};
};
```

## File Import Pattern

```typescript
// Barrel exports
export { Button } from "./Button";
export { Input } from "./Input";
export { Select } from "./Select";

// Usage
import { Button, Input, Select } from "@/components/ui";
```

## Type Definition Pattern

```typescript
// Simple types (use type, not interface)
export type NameCategory = "person_names" | "place_names" | "artifact_names";

export type SyllableGroups = {
	[key: string]: string[];
};

// Complex types that might be extended (use interface)
export interface NameGeneratorState {
	currentLanguage: string;
	names: string[];
	isLoading: boolean;
	error: string | null;
}
```
