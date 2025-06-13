# Language Data Structure

## Overview

The fantasy name generator uses JSON files to define languages with syllable-based name generation patterns. Each language contains syllable groups and patterns that determine how names are constructed.

## File Organization

### Language Metadata

**File**: `src/assets/data/language-metadata.json`

```json
{
	"aetherian": {
		"id": "aetherian",
		"name": "Aetherian",
		"description": "An ancient highland language with strong consonants and flowing vowels, inspired by Celtic and Germanic traditions.",
		"themes": ["highland", "mountain", "ancient"],
		"difficulty": "beginner",
		"author": "Fantasy Name Generator",
		"version": "1.0.0",
		"tags": ["fantasy", "medieval", "celtic-inspired"],
		"categories": ["person_names", "place_names", "family_names"],
		"sampleNames": {
			"person_names": ["Karandor", "Morwen", "Dunaleth"],
			"place_names": ["Karheim", "Morburg", "Dunsted"]
		}
	}
}
```

### Individual Language Files

**Path**: `src/assets/data/languages/[language-id].json`

## Language Structure

### Complete Language Schema

```typescript
export type FantasyLanguage = {
	// Basic metadata
	id: string;
	name: string;
	description: string;
	version: string;

	// Syllable groups - the building blocks
	syllables: {
		[groupName: string]: string[];
	};

	// Generation patterns
	patterns: {
		[category: string]: NamePattern[];
	};

	// Optional advanced features
	phonotactics?: {
		allowedClusters?: string[];
		forbiddenCombinations?: string[];
		vowelHarmony?: boolean;
	};

	// Metadata for UI
	metadata?: {
		themes: string[];
		difficulty: "beginner" | "intermediate" | "advanced";
		culturalInspiration?: string;
	};
};

export type NamePattern = {
	pattern: string; // e.g., "A.B.C"
	weight: number; // Probability weight
	description?: string;
	examples?: string[];
};
```

## Syllable Group Design

### Group Naming Convention

-  **A, B, C, D, E...**: Primary syllable groups
-  **Descriptive names**: Optional for clarity (beginnings, middles, endings)

### Group Characteristics

```json
{
	"syllables": {
		"A": ["kar", "mor", "dun", "val", "thor"], // Strong starting sounds
		"B": ["an", "eth", "or", "in", "al"], // Short connecting sounds
		"C": ["dor", "wen", "ith", "ton", "rim"], // Ending sounds
		"D": ["el", "ar", "ul"], // Short vowel-heavy
		"E": ["heim", "burg", "sted", "haven"], // Place name endings
		"F": ["-stone", "-fell", "-ridge", "-wood"] // Compound endings with hyphens
	}
}
```

## Pattern System

### Pattern Syntax

-  **Groups**: Letters (A, B, C, D, E, F...)
-  **Separator**: Dot (.) to separate groups
-  **Examples**: "A.B", "A.B.C", "A.D.E", "A.F"

### Pattern Weights

-  **Higher weight** = more common
-  **Lower weight** = less common
-  **Total doesn't need to equal 100**

```json
{
	"patterns": {
		"person_names": [
			{
				"pattern": "A.B",
				"weight": 40,
				"description": "Simple two-syllable names"
			},
			{
				"pattern": "A.B.C",
				"weight": 35,
				"description": "Classic three-syllable names"
			},
			{
				"pattern": "A.D.C",
				"weight": 25,
				"description": "Names with vowel-heavy middle"
			}
		],
		"place_names": [
			{
				"pattern": "A.E",
				"weight": 50,
				"description": "Traditional place names"
			},
			{
				"pattern": "A.B.E",
				"weight": 30,
				"description": "Longer place names"
			},
			{
				"pattern": "A.F",
				"weight": 20,
				"description": "Compound place names"
			}
		]
	}
}
```

## Example Language Files

### Aetherian (Highland Language)

```json
{
	"id": "aetherian",
	"name": "Aetherian",
	"description": "Ancient highland language with Celtic and Germanic influences",
	"version": "1.0.0",

	"syllables": {
		"A": ["kar", "mor", "dun", "val", "thor", "bran", "erik", "ulf"],
		"B": ["an", "eth", "or", "in", "al", "ur", "ar", "en"],
		"C": ["dor", "wen", "ith", "ton", "rim", "gar", "mund", "ric"],
		"D": ["el", "ar", "ul", "il", "ol"],
		"E": ["heim", "burg", "sted", "havn", "mark", "land"],
		"F": ["-stone", "-fell", "-ridge", "-brook", "-wood", "-moor"]
	},

	"patterns": {
		"person_names": [
			{ "pattern": "A.B", "weight": 40 },
			{ "pattern": "A.B.C", "weight": 35 },
			{ "pattern": "A.D.C", "weight": 25 }
		],
		"place_names": [
			{ "pattern": "A.E", "weight": 50 },
			{ "pattern": "A.B.E", "weight": 30 },
			{ "pattern": "A.F", "weight": 20 }
		],
		"family_names": [
			{ "pattern": "A.C", "weight": 60 },
			{ "pattern": "A.B.C", "weight": 40 }
		]
	},

	"metadata": {
		"themes": ["highland", "mountain", "ancient"],
		"difficulty": "beginner",
		"culturalInspiration": "Celtic and Germanic traditions"
	}
}
```

### Nymeran (Coastal Language)

```json
{
	"id": "nymeran",
	"name": "Nymeran",
	"description": "Flowing coastal language with maritime influences",
	"version": "1.0.0",

	"syllables": {
		"A": ["ael", "mer", "thal", "nyl", "lyra", "coral", "pearl"],
		"B": ["ia", "ea", "ae", "ei", "ou", "au"],
		"C": ["rin", "lon", "mir", "del", "lyn", "tha", "ros"],
		"D": ["os", "is", "as", "us"],
		"E": ["haven", "port", "bay", "cove", "reef", "tide"],
		"F": ["-song", "-wave", "-pearl", "-shell", "-deep"]
	},

	"patterns": {
		"person_names": [
			{ "pattern": "A.B.C", "weight": 45 },
			{ "pattern": "A.C", "weight": 35 },
			{ "pattern": "A.D.C", "weight": 20 }
		],
		"place_names": [
			{ "pattern": "A.E", "weight": 60 },
			{ "pattern": "A.B.E", "weight": 40 }
		],
		"ship_names": [
			{ "pattern": "A.F", "weight": 70 },
			{ "pattern": "A.B.F", "weight": 30 }
		]
	},

	"phonotactics": {
		"vowelHarmony": true,
		"allowedClusters": ["th", "sh", "ch", "ng"],
		"forbiddenCombinations": ["xx", "qq"]
	},

	"metadata": {
		"themes": ["coastal", "maritime", "flowing"],
		"difficulty": "intermediate",
		"culturalInspiration": "Mediterranean and Polynesian influences"
	}
}
```

## Advanced Features

### Phonotactics (Optional)

```json
{
	"phonotactics": {
		"vowelHarmony": true,
		"allowedClusters": ["st", "tr", "kr", "th", "sh"],
		"forbiddenCombinations": ["qx", "zx", "cq"],
		"syllableStructure": "CV|CVC|CVCC"
	}
}
```

### Contextual Variations

```json
{
	"contextualRules": {
		"male_names": {
			"favoredEndings": ["C", "D"],
			"weightModifier": 1.2
		},
		"female_names": {
			"favoredEndings": ["B", "soft_C"],
			"weightModifier": 1.1
		}
	}
}
```

## Data Validation

### Required Fields

-  `id`: Unique identifier
-  `name`: Display name
-  `syllables`: At least one syllable group
-  `patterns`: At least one pattern category

### Validation Rules

-  **Syllable groups**: Must contain at least 3 syllables each
-  **Patterns**: Must reference existing syllable groups
-  **Weights**: Must be positive numbers
-  **IDs**: Must be valid filenames (lowercase, no spaces)

### Error Handling

-  **Missing syllable groups**: Fallback to default patterns
-  **Invalid patterns**: Skip invalid patterns, log warnings
-  **Malformed JSON**: Show error message, prevent app crash
-  **Missing language files**: Graceful degradation

## Performance Considerations

### File Size Optimization

-  **Compress repetitive data**: Use shorter syllable names when possible
-  **Efficient patterns**: Avoid overly complex pattern structures
-  **JSON minification**: Remove whitespace in production
-  **Lazy loading**: Only load languages when needed

### Memory Usage

-  **Cache loaded languages**: Keep in memory after first load
-  **Cleanup unused**: Remove languages not accessed recently
-  **Efficient structures**: Use arrays over objects when possible

## Creating New Languages

### Step-by-Step Process

1. **Research cultural inspiration**: Study real-world language families
2. **Define phonetic character**: Choose sounds and syllable structures
3. **Create syllable groups**: Design 5-8 groups with 8-15 syllables each
4. **Design patterns**: Create 3-5 patterns per category with appropriate weights
5. **Test generation**: Generate sample names and refine
6. **Add metadata**: Complete description and categorization
7. **Validate structure**: Ensure JSON is valid and follows schema

### Quality Guidelines

-  **Consistency**: Syllables should sound like they belong together
-  **Variety**: Enough variation to generate diverse names
-  **Balance**: Patterns should create names of varying lengths
-  **Authenticity**: Should feel like a real language family
-  **Memorability**: Generated names should be pronounceable

### Testing New Languages

```javascript
// Generate test batch
const testNames = [];
for (let i = 0; i < 50; i++) {
	testNames.push(generateName(language, "person_names"));
}

// Check for quality
const checks = {
	uniqueness: new Set(testNames).size / testNames.length,
	averageLength:
		testNames.reduce((sum, name) => sum + name.length, 0) / testNames.length,
	pronounceable: testNames.every((name) => /^[a-zA-Z-]+$/.test(name)),
};
```

## Language Expansion Strategy

### Phase 1 Languages (Launch)

1. **Aetherian** - Highland/Celtic
2. **Nymeran** - Coastal/Mediterranean
3. **Drakonic** - Harsh/Germanic
4. **Sylvani** - Forest/Elvish
5. **Khemaran** - Desert/Arabic-inspired

### Phase 2 Languages (Post-launch)

1. **Nordiac** - Arctic/Norse
2. **Valerian** - Imperial/Latin
3. **Shadowspeak** - Dark/Gothic
4. **Celestian** - Ethereal/Angelic
5. **Primordial** - Ancient/Mysterious

### Community Languages (Future)

-  **User-created**: System for users to create and share languages
-  **Validation**: Community voting and moderation
-  **Import/Export**: Standard format for sharing
-  **Remix**: Allow modification of existing languages

## Localization Considerations

### Internationalization Support

-  **Unicode support**: Handle non-ASCII characters in syllables
-  **RTL languages**: Support right-to-left text if needed
-  **Accent marks**: Preserve diacritical marks in generated names
-  **Character limits**: Consider display limitations

### Cultural Sensitivity

-  **Respectful inspiration**: Avoid direct copying of real cultures
-  **Generic fantasy**: Keep languages clearly fictional
-  **Inclusive representation**: Diverse cultural inspirations
-  **Community feedback**: Listen to cultural concerns

## Version Control

### Language Versioning

-  **Semantic versioning**: Major.minor.patch (e.g., 1.2.1)
-  **Backward compatibility**: Maintain compatibility with older versions
-  **Migration paths**: Handle breaking changes gracefully
-  **Changelog**: Document changes between versions

### Data Migration

```typescript
const migrateLanguage = (data: any, fromVersion: string, toVersion: string) => {
	switch (fromVersion) {
		case "1.0.0":
			// Add new required fields
			if (!data.metadata) {
				data.metadata = { themes: [], difficulty: "intermediate" };
			}
			break;
		case "1.1.0":
			// Handle pattern format changes
			data.patterns = convertPatternFormat(data.patterns);
			break;
	}
	return data;
};
```
