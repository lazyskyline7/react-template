# Agent Guidelines for aip-pratice

This document provides essential information for AI coding agents working in this React + TypeScript + Vite codebase.

## Build, Lint, and Test Commands

### Development

```bash
pnpm dev              # Start dev server (http://localhost:5173)
pnpm build            # Type check + production build
pnpm preview          # Preview production build
```

### Testing

```bash
pnpm test             # Run tests in watch mode
pnpm run test:run     # Run all tests once (CI mode)
pnpm run test:ui      # Open Vitest UI
pnpm run test:coverage # Run tests with coverage report
```

### Running a Single Test File

```bash
pnpm test src/__tests__/App.test.tsx           # Watch mode for specific file
pnpm run test:run src/__tests__/App.test.tsx   # Run once
```

### Running a Single Test Case

```bash
pnpm test -t "renders heading"                 # By test name (watch mode)
pnpm run test:run -t "renders heading"         # By test name (run once)
```

### Code Quality

```bash
pnpm run lint         # Check code with ESLint
pnpm run format       # Format code with Prettier
pnpm run format:check # Check if code is formatted
```

## Project Structure

```
src/
├── __tests__/        # Test files (*.test.tsx, *.test.ts)
├── components/       # React components
│   └── ui/          # shadcn/ui components (exempt from react-refresh rules)
├── lib/             # Utility functions and helpers
├── assets/          # Static assets
├── App.tsx          # Root component
├── main.tsx         # Application entry point
└── globals.css      # Global styles (Tailwind base)
```

## Code Style Guidelines

### Import Organization

**Order**: React/third-party imports → local imports

```typescript
// ✅ Good
import * as React from "react";
import { Slot } from "@radix-ui/react-slot";
import { cva, type VariantProps } from "class-variance-authority";

import { cn } from "@/lib/utils";

// ❌ Bad - mixed ordering
import { cn } from "@/lib/utils";
import * as React from "react";
```

### Import Style

- Use **named imports** by default: `import { useState } from "react"`
- Use **namespace imports** for React when using many exports: `import * as React from "react"`
- Use **type imports** with inline `type` keyword: `import { type ClassValue } from "clsx"`
- Use **@/ alias** for src/ imports: `import { cn } from "@/lib/utils"`

### Component Patterns

**Simple components**: Use function declarations

```typescript
// ✅ Good
function App() {
  return <div>Hello</div>;
}

export default App;
```

**Complex UI components**: Use `React.forwardRef` with const

```typescript
// ✅ Good
const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, ...props }, ref) => {
    return <button ref={ref} {...props} />;
  }
);
Button.displayName = "Button";

export { Button };
```

**NEVER use**: `React.FC` or `React.FunctionComponent`

### Props and Types

**Use interfaces** for component props:

```typescript
// ✅ Good
export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>, VariantProps<typeof buttonVariants> {
  asChild?: boolean;
}

// ❌ Bad - inline types
const Button = (props: { variant?: string; children: React.ReactNode }) => {};
```

**Extend HTML attributes** when wrapping native elements:

```typescript
export interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  // custom props
}
```

### Naming Conventions

- **Components**: PascalCase (`Button`, `UserProfile`)
- **Functions/variables**: camelCase (`handleClick`, `isLoading`)
- **Constants**: camelCase or UPPER_SNAKE_CASE for true constants
- **Files**: Match component names (`Button.tsx`), camelCase for utilities (`utils.ts`)
- **Test files**: `*.test.tsx` or `*.test.ts` in `__tests__/` directory

### TypeScript

**Strict mode enabled** - all strict checks are on:

```typescript
// tsconfig.json enforces:
- strict: true
- noUnusedLocals: true
- noUnusedParameters: true
- noFallthroughCasesInSwitch: true
```

**NEVER suppress type errors**:

```typescript
// ❌ NEVER do this
const value = data as any;
// @ts-ignore
const result = somethingWrong();
// @ts-expect-error
const x = invalid;
```

**Handle null/undefined explicitly**:

```typescript
// ✅ Good
const rootElement = document.getElementById("root");
if (!rootElement) throw new Error("Root element not found");
createRoot(rootElement).render(<App />);

// ✅ Also acceptable with non-null assertion IF you're certain
createRoot(document.getElementById("root")!).render(<App />);
```

### Styling

**Use Tailwind CSS** for all styling:

```typescript
// ✅ Good
<div className="flex min-h-screen items-center justify-center bg-slate-50">

// ✅ For dynamic classes, use cn utility
import { cn } from "@/lib/utils";
<button className={cn("base-class", variant === "primary" && "primary-class")}>
```

**Class ordering**: Prettier with `prettier-plugin-tailwindcss` handles this automatically.

### Utilities

**Place utility functions in `src/lib/`**:

```typescript
// src/lib/utils.ts
import { type ClassValue, clsx } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

## Testing Guidelines

### Framework: Vitest + React Testing Library

**Test file structure**:

```typescript
import { describe, it, expect } from "vitest";
import { render, screen } from "@testing-library/react";
import ComponentName from "../ComponentName";

describe("ComponentName", () => {
  it("renders correctly", () => {
    render(<ComponentName />);
    expect(screen.getByText(/expected text/i)).toBeInTheDocument();
  });
});
```

**Available matchers**: Extended with `@testing-library/jest-dom` (configured in `vitest.setup.ts`)

- `toBeInTheDocument()`
- `toHaveTextContent()`
- `toBeVisible()`
- etc.

**Cleanup**: Automatic via `vitest.setup.ts` - no manual cleanup needed.

## ESLint Rules

**Key rules**:

- `react-refresh/only-export-components`: warn (except for `src/components/ui/**`)
- React Hooks rules: enforced via `eslint-plugin-react-hooks`
- TypeScript recommended rules: enabled

**ui/ components are exempt** from react-refresh rules (shadcn/ui pattern).

## Prettier Configuration

```json
{
  "semi": true,
  "singleQuote": false,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 100,
  "arrowParens": "always",
  "endOfLine": "lf"
}
```

**Run before committing**: `pnpm run format`

## Common Patterns

### Error Handling

Handle errors explicitly - no silent failures:

```typescript
// ✅ Good
try {
  await riskyOperation();
} catch (error) {
  console.error("Operation failed:", error);
  // Handle appropriately
}

// ❌ Bad - empty catch
try {
  await riskyOperation();
} catch (e) {}
```

### Variant Components (shadcn/ui pattern)

Use `class-variance-authority` for variant-based components:

```typescript
import { cva, type VariantProps } from "class-variance-authority";

const buttonVariants = cva("base-classes", {
  variants: {
    variant: { default: "...", destructive: "..." },
    size: { default: "...", sm: "...", lg: "..." },
  },
  defaultVariants: { variant: "default", size: "default" },
});
```

## Notes

- **Package manager:** pnpm (NEVER use npm or yarn)
- **Node version:** Modern ES2020+; target is ESNext via Vite.
- **Module system:** ES modules with Vite bundler.
- **CSS:** Tailwind CSS v4 configured through Vite plugin.
- **Path alias:** `@/` points to `src/`.
- **Shadcn/ui components** live in `src/components/ui` and are exempt from react-refresh lint rules.


## Verification Checklist

Before marking any task complete:

- [ ] Run `pnpm run lint` - must pass
- [ ] Run `pnpm run format:check` - must pass
- [ ] Run `pnpm run test:run` - all tests pass
- [ ] Run `pnpm build` - must complete successfully
- [ ] No TypeScript errors (no `as any`, `@ts-ignore`, etc.)
- [ ] Imports follow the established order
- [ ] Components follow the appropriate pattern (function declaration vs forwardRef)

## Notes

- **Package manager**: pnpm (NEVER use npm or yarn)
- **Node version**: Uses modern ES2020+ features
- **Module system**: ESNext with bundler resolution
- **CSS**: Tailwind v4 via Vite plugin
- **Path alias**: `@/` maps to `src/`
