# SCSS/BEM Styling Guide (Mobile First)

## Mobile First Philosophy

### Design Process

1. **Start with mobile** (320px and up)
2. **Add tablet styles** (768px and up)
3. **Enhance for desktop** (1024px and up)
4. **Optimize for large screens** (1440px and up)

### Breakpoint Strategy

```scss
// styles/abstracts/_variables.scss
$breakpoints: (
	"tablet": 768px,
	"desktop": 1024px,
	"wide": 1440px,
);

// Mixin for consistent breakpoints
@mixin respond-to($breakpoint) {
	$value: map-get($breakpoints, $breakpoint);
	@media (min-width: $value) {
		@content;
	}
}
```

## SCSS Architecture with @use

### File Structure

```scss
// styles/main.scss - Main entry point
@use "abstracts/variables";
@use "abstracts/mixins";
@use "base/reset";
@use "base/typography";
@use "layout/grid";
@use "themes/light";
```

### Variables (@use pattern)

```scss
// styles/abstracts/_variables.scss
:root {
	// Colors
	--color-primary: #2563eb;
	--color-primary-light: #3b82f6;
	--color-primary-dark: #1d4ed8;

	--color-text: #1f2937;
	--color-text-light: #6b7280;
	--color-background: #ffffff;

	// Spacing (mobile first)
	--spacing-xs: 0.25rem;
	--spacing-sm: 0.5rem;
	--spacing-md: 1rem;
	--spacing-lg: 1.5rem;
	--spacing-xl: 2rem;

	// Typography (mobile first)
	--font-size-sm: 0.875rem;
	--font-size-base: 1rem;
	--font-size-lg: 1.125rem;
	--font-size-xl: 1.25rem;
	--font-size-2xl: 1.5rem;

	// Responsive spacing
	@media (min-width: 768px) {
		--spacing-lg: 2rem;
		--spacing-xl: 3rem;
	}

	@media (min-width: 1024px) {
		--font-size-base: 1.125rem;
		--font-size-lg: 1.25rem;
	}
}

// Export for use in other files
$color-primary: var(--color-primary);
$spacing-md: var(--spacing-md);
```

### Mixins

```scss
// styles/abstracts/_mixins.scss
@use "variables" as vars;

// Button mixin with mobile-first approach
@mixin button-base {
	display: inline-flex;
	align-items: center;
	justify-content: center;
	padding: vars.$spacing-sm vars.$spacing-md;
	border: none;
	border-radius: 0.375rem;
	font-size: var(--font-size-base);
	font-weight: 500;
	text-decoration: none;
	cursor: pointer;
	transition: all 0.2s ease-in-out;

	// Mobile: Full width by default
	width: 100%;
	min-height: 44px; // Touch target size

	// Tablet and up: Auto width
	@include respond-to("tablet") {
		width: auto;
		min-width: 120px;
	}

	&:disabled {
		opacity: 0.6;
		cursor: not-allowed;
	}
}

// Touch-friendly interactive element
@mixin touch-target {
	min-height: 44px;
	min-width: 44px;
	padding: vars.$spacing-sm;

	@include respond-to("desktop") {
		min-height: 32px;
		min-width: 32px;
	}
}

// Mobile-first container
@mixin container {
	width: 100%;
	padding-left: var(--spacing-md);
	padding-right: var(--spacing-md);
	margin-left: auto;
	margin-right: auto;

	@include respond-to("tablet") {
		padding-left: var(--spacing-lg);
		padding-right: var(--spacing-lg);
	}

	@include respond-to("desktop") {
		max-width: 1200px;
	}

	@include respond-to("wide") {
		max-width: 1400px;
	}
}
```

## BEM Methodology

### Block-Element-Modifier Structure

```scss
// Block
.name-generator {
	@include container;

	// Element
	&__header {
		margin-bottom: var(--spacing-lg);

		@include respond-to("tablet") {
			display: flex;
			align-items: center;
			justify-content: space-between;
		}
	}

	&__title {
		font-size: var(--font-size-2xl);
		font-weight: 700;
		color: var(--color-text);
		margin: 0 0 var(--spacing-sm);

		@include respond-to("tablet") {
			margin: 0;
		}
	}

	&__controls {
		display: flex;
		flex-direction: column;
		gap: var(--spacing-md);

		@include respond-to("tablet") {
			flex-direction: row;
			align-items: center;
		}
	}

	&__main {
		display: grid;
		gap: var(--spacing-lg);

		@include respond-to("tablet") {
			grid-template-columns: 1fr 2fr;
			gap: var(--spacing-xl);
		}
	}

	// Modifier
	&--compact {
		.name-generator__main {
			gap: var(--spacing-md);
		}
	}

	&--loading {
		.name-generator__controls {
			opacity: 0.6;
			pointer-events: none;
		}
	}
}
```

### Component-Specific Patterns

#### Button Component

```scss
// components/ui/Button/Button.scss
@use "../../../styles/abstracts/mixins" as mixins;
@use "../../../styles/abstracts/variables" as vars;

.button {
	@include mixins.button-base;

	// Variants
	&--primary {
		background-color: var(--color-primary);
		color: white;

		&:hover:not(:disabled) {
			background-color: var(--color-primary-dark);
		}

		&:active {
			transform: translateY(1px);
		}
	}

	&--secondary {
		background-color: transparent;
		color: var(--color-primary);
		border: 2px solid var(--color-primary);

		&:hover:not(:disabled) {
			background-color: var(--color-primary);
			color: white;
		}
	}

	&--danger {
		background-color: #dc2626;
		color: white;

		&:hover:not(:disabled) {
			background-color: #b91c1c;
		}
	}

	// Sizes
	&--small {
		padding: vars.$spacing-xs vars.$spacing-sm;
		font-size: var(--font-size-sm);
		min-height: 36px;

		@include respond-to("desktop") {
			min-height: 28px;
		}
	}

	&--large {
		padding: vars.$spacing-md vars.$spacing-lg;
		font-size: var(--font-size-lg);
		min-height: 52px;

		@include respond-to("desktop") {
			min-height: 44px;
		}
	}

	// States
	&--loading {
		position: relative;
		color: transparent;

		&::after {
			content: "";
			position: absolute;
			top: 50%;
			left: 50%;
			transform: translate(-50%, -50%);
			width: 20px;
			height: 20px;
			border: 2px solid currentColor;
			border-radius: 50%;
			border-top-color: transparent;
			animation: spin 1s linear infinite;
		}
	}
}

@keyframes spin {
	to {
		transform: translate(-50%, -50%) rotate(360deg);
	}
}
```

#### Input Component

```scss
// components/ui/Input/Input.scss
@use "../../../styles/abstracts/mixins" as mixins;

.input {
	&__wrapper {
		display: flex;
		flex-direction: column;
		gap: var(--spacing-xs);
	}

	&__label {
		font-size: var(--font-size-sm);
		font-weight: 500;
		color: var(--color-text);
	}

	&__field {
		width: 100%;
		padding: var(--spacing-sm) var(--spacing-md);
		border: 2px solid #e5e7eb;
		border-radius: 0.375rem;
		font-size: var(--font-size-base);
		background-color: var(--color-background);
		transition: border-color 0.2s ease-in-out;

		// Mobile: Larger touch targets
		min-height: 44px;

		@include respond-to("desktop") {
			min-height: 40px;
		}

		&:focus {
			outline: none;
			border-color: var(--color-primary);
			box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.1);
		}

		&::placeholder {
			color: var(--color-text-light);
		}

		&:disabled {
			background-color: #f9fafb;
			color: var(--color-text-light);
			cursor: not-allowed;
		}
	}

	&__error {
		font-size: var(--font-size-sm);
		color: #dc2626;
		margin-top: var(--spacing-xs);
	}

	// Variants
	&--error {
		.input__field {
			border-color: #dc2626;

			&:focus {
				border-color: #dc2626;
				box-shadow: 0 0 0 3px rgba(220, 38, 38, 0.1);
			}
		}
	}

	&--success {
		.input__field {
			border-color: #16a34a;
		}
	}
}
```

## Mobile-First Layout Patterns

### Grid System

```scss
// styles/layout/_grid.scss
.grid {
	display: grid;
	gap: var(--spacing-md);

	// Mobile: Single column
	grid-template-columns: 1fr;

	// Tablet: Two columns
	@include respond-to("tablet") {
		grid-template-columns: repeat(2, 1fr);
		gap: var(--spacing-lg);
	}

	// Desktop: Three columns
	@include respond-to("desktop") {
		grid-template-columns: repeat(3, 1fr);
		gap: var(--spacing-xl);
	}

	// Modifiers for specific layouts
	&--auto-fit {
		grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
	}

	&--sidebar {
		@include respond-to("tablet") {
			grid-template-columns: 1fr 2fr;
		}

		@include respond-to("desktop") {
			grid-template-columns: 300px 1fr;
		}
	}
}

.grid-item {
	// Full width on mobile
	grid-column: 1 / -1;

	&--span-2 {
		@include respond-to("tablet") {
			grid-column: span 2;
		}
	}

	&--span-3 {
		@include respond-to("desktop") {
			grid-column: span 3;
		}
	}
}
```

### Flexbox Utilities

```scss
// styles/layout/_flex.scss
.flex {
	display: flex;

	&--column {
		flex-direction: column;

		@include respond-to("tablet") {
			flex-direction: row;
		}
	}

	&--center {
		align-items: center;
		justify-content: center;
	}

	&--between {
		justify-content: space-between;
	}

	&--wrap {
		flex-wrap: wrap;
	}

	&--gap-sm {
		gap: var(--spacing-sm);
	}

	&--gap-md {
		gap: var(--spacing-md);
	}

	&--gap-lg {
		gap: var(--spacing-lg);
	}
}
```

## Responsive Typography

### Mobile-First Typography Scale

```scss
// styles/base/_typography.scss
:root {
	// Mobile typography
	--font-size-xs: 0.75rem;
	--font-size-sm: 0.875rem;
	--font-size-base: 1rem;
	--font-size-lg: 1.125rem;
	--font-size-xl: 1.25rem;
	--font-size-2xl: 1.5rem;
	--font-size-3xl: 1.875rem;

	// Line heights
	--line-height-tight: 1.25;
	--line-height-normal: 1.5;
	--line-height-relaxed: 1.75;

	// Tablet adjustments
	@include respond-to("tablet") {
		--font-size-base: 1.125rem;
		--font-size-lg: 1.25rem;
		--font-size-xl: 1.5rem;
		--font-size-2xl: 1.875rem;
		--font-size-3xl: 2.25rem;
	}

	// Desktop adjustments
	@include respond-to("desktop") {
		--font-size-2xl: 2rem;
		--font-size-3xl: 2.5rem;
	}
}

.heading {
	font-weight: 700;
	line-height: var(--line-height-tight);
	color: var(--color-text);

	&--1 {
		font-size: var(--font-size-3xl);
		margin-bottom: var(--spacing-lg);
	}

	&--2 {
		font-size: var(--font-size-2xl);
		margin-bottom: var(--spacing-md);
	}

	&--3 {
		font-size: var(--font-size-xl);
		margin-bottom: var(--spacing-sm);
	}
}

.text {
	line-height: var(--line-height-normal);
	color: var(--color-text);

	&--small {
		font-size: var(--font-size-sm);
	}

	&--large {
		font-size: var(--font-size-lg);
	}

	&--muted {
		color: var(--color-text-light);
	}
}
```

## Animation and Transitions

### Mobile-Optimized Animations

```scss
// Respect user preferences
@media (prefers-reduced-motion: reduce) {
	*,
	*::before,
	*::after {
		animation-duration: 0.01ms !important;
		animation-iteration-count: 1 !important;
		transition-duration: 0.01ms !important;
		scroll-behavior: auto !important;
	}
}

// Smooth transitions
.transition {
	transition-property: color, background-color, border-color, opacity,
		transform;
	transition-duration: 0.2s;
	transition-timing-function: ease-in-out;

	&--slow {
		transition-duration: 0.3s;
	}

	&--fast {
		transition-duration: 0.1s;
	}
}

// Hover effects (desktop only)
@include respond-to("desktop") {
	.hover-lift {
		&:hover {
			transform: translateY(-2px);
			box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
		}
	}

	.hover-scale {
		&:hover {
			transform: scale(1.05);
		}
	}
}
```

## Theme Support

### CSS Custom Properties for Theming

```scss
// styles/themes/_light.scss
:root {
	--color-background: #ffffff;
	--color-surface: #f8fafc;
	--color-text: #1f2937;
	--color-text-light: #6b7280;
	--color-border: #e5e7eb;

	--shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
	--shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
	--shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
}

// styles/themes/_dark.scss
[data-theme="dark"] {
	--color-background: #111827;
	--color-surface: #1f2937;
	--color-text: #f9fafb;
	--color-text-light: #9ca3af;
	--color-border: #374151;

	--shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.3);
	--shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.4);
	--shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.4);
}
```

## Performance Best Practices

### Efficient SCSS

```scss
// Good - Use mixins for repeated patterns
@mixin card-shadow {
	box-shadow: var(--shadow-md);

	&:hover {
		box-shadow: var(--shadow-lg);
	}
}

// Good - Use CSS custom properties for dynamic values
.dynamic-component {
	color: var(--dynamic-color, var(--color-text));
	background-color: var(--dynamic-bg, var(--color-background));
}

// Bad - Avoid deep nesting (max 3 levels)
.component {
	.nested {
		.too {
			.deep {
				// Avoid this
			}
		}
	}
}

// Good - Keep nesting shallow
.component {
	&__element {
		&--modifier {
			// This is fine
		}
	}
}
```

### Critical CSS Strategy

```scss
// Load critical styles first
@use "base/reset";
@use "base/typography";
@use "layout/grid";

// Load component styles after critical path
@use "components/button";
@use "components/input";
```
