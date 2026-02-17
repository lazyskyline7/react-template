# React Development Environment

Modern React application with TypeScript, Vite, and comprehensive testing setup.

## Setup Complete ✅

- ✅ React + TypeScript
- ✅ Vite (blazing fast dev server)
- ✅ Vitest (reliable testing with jsdom)
- ✅ React Testing Library
- ✅ Tailwind CSS
- ✅ Prettier with Tailwind plugin
- ✅ ESLint + Prettier integration (zero conflicts!)
- ✅ pnpm (fast, efficient package manager)

## Available Scripts

```bash
pnpm dev              # Start dev server
pnpm build            # Build for production
pnpm preview          # Preview production build
pnpm test             # Run tests in watch mode
pnpm run test:run     # Run tests once
pnpm run test:ui      # Open Vitest UI
pnpm run test:coverage # Run tests with coverage
pnpm run format       # Format code with Prettier
pnpm run format:check # Check formatting
pnpm run lint         # Run ESLint
```

## Quick Start

1. Start development server:
   ```bash
   pnpm dev
   ```

2. Open [http://localhost:5173](http://localhost:5173)

3. Run tests:
   ```bash
   pnpm test
   ```

## Project Structure

```
src/
├── __tests__/
│   └── App.test.tsx
├── App.tsx
└── main.tsx
```

## Testing

Tests use Vitest with jsdom for reliable DOM testing.

### Running Tests

```bash
# Watch mode (recommended for development)
pnpm test

# Run once
pnpm run test:run

# With UI
pnpm run test:ui

# With coverage
pnpm run test:coverage
```

## Ready to go!

Your development environment includes:
- Fast iteration (Vite)
- Efficient package management (pnpm)
- Reliable testing (Vitest + jsdom)
- Type safety (TypeScript)
- Modern styling (Tailwind)
- Code quality (ESLint + Prettier)
