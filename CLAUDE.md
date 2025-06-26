# ToDo App - Development Guide

## Overview
A modern ToDo application built with React Router v7 for seamless navigation, deployed on Cloudflare Workers for edge performance. The project emphasizes type safety, comprehensive testing, and optimal user experience.

**Tech Stack:**
- **Framework**: React Router v7 (Remix-based)
- **Language**: TypeScript
- **Styling**: CSS Modules / Tailwind CSS
- **Testing**: Vitest (unit/integration), Playwright (E2E)
- **Deployment**: Cloudflare Workers & Pages
- **Database**: Cloudflare D1 / KV Store
- **Build Tool**: Vite

## Commands

### Development
```bash
npm run dev          # Start development server (localhost:3000)
npm run build        # Build for production
npm run preview      # Preview production build locally
npm run typecheck    # Run TypeScript type checking
```

### Testing
```bash
npm run test         # Run unit tests with Vitest
npm run test:watch   # Run tests in watch mode
npm run test:coverage # Generate test coverage report
npm run test:e2e     # Run Playwright E2E tests
npm run test:e2e:ui  # Run Playwright tests with UI
```

### Code Quality
```bash
npm run lint         # Run ESLint
npm run lint:fix     # Auto-fix ESLint issues
npm run format       # Format code with Prettier
npm run format:check # Check code formatting
```

### Deployment
```bash
npm run deploy       # Deploy to Cloudflare Workers
npm run deploy:preview # Deploy to preview environment
npm run tail         # Tail production logs
wrangler dev         # Local Cloudflare Workers development
```

## Project Structure
```
src/
├── routes/          # React Router v7 route modules
│   ├── _index.tsx   # Home route (todo list)
│   ├── todo.$id.tsx # Individual todo detail
│   └── _layout.tsx  # Root layout component
├── components/      # Reusable React components
│   ├── TodoItem.tsx
│   ├── TodoList.tsx
│   └── TodoForm.tsx
├── lib/            # Business logic and utilities
│   ├── todos.server.ts  # Server-side todo operations
│   └── db.server.ts     # Database connection
├── types/          # TypeScript type definitions
├── styles/         # Global styles and CSS modules
└── tests/          # Test utilities and setup
app/
├── entry.client.tsx # Client entry point
├── entry.server.tsx # Server entry point
└── root.tsx        # Root component
```

## Code Style Guidelines

### TypeScript
- Enable strict mode in tsconfig.json
- Prefer interfaces over type aliases for object shapes
- Use explicit return types for functions
- Avoid `any` type - use `unknown` when type is truly unknown

### React Components
- Use function components with TypeScript
- Implement proper error boundaries
- Use React Router's data APIs (loader/action) for data fetching
- Colocate component files with their tests and styles

### File Naming
- Components: PascalCase (e.g., `TodoItem.tsx`)
- Utilities: camelCase (e.g., `formatDate.ts`)
- Server modules: `.server.ts` suffix
- Test files: `.test.ts` or `.spec.ts`

### Imports
- Use absolute imports from `~/` (configured in tsconfig.json)
- Group imports: external deps, internal modules, types, styles
- Sort imports alphabetically within groups

## Development Workflow

### Feature Development
1. Create feature branch: `git checkout -b feature/description`
2. Write tests first (TDD approach)
3. Implement feature following existing patterns
4. Ensure all tests pass: `npm run test`
5. Check types: `npm run typecheck`
6. Fix linting issues: `npm run lint:fix`
7. Create PR with clear description

### Development Best Practices
- Run typecheck and lint after completing tasks and be sure they ALWAYS pass.

### Data Flow
1. **Routes** define loaders (data fetching) and actions (mutations)
2. **Components** consume data via `useLoaderData()` and `useFetcher()`
3. **Server modules** handle database operations and business logic
4. **Cloudflare Workers** provide edge computing and caching

### Testing Strategy
- **Unit Tests**: Test utilities and pure functions with Vitest
- **Integration Tests**: Test React components with Testing Library
- **E2E Tests**: Test user flows with Playwright
- Maintain >80% code coverage
- Test error states and edge cases

## Cloudflare Workers Specifics

### Environment Variables
```toml
# wrangler.toml
[vars]
ENVIRONMENT = "production"

[[d1_databases]]
binding = "DB"
database_name = "todo-app"
database_id = "xxx-xxx-xxx"

[[kv_namespaces]]
binding = "CACHE"
id = "xxx-xxx-xxx"
```

### Workers Limitations
- 10ms CPU time limit (50ms for paid plans)
- 128MB memory limit
- Use streaming responses for large data
- Implement proper error handling for edge cases

### Caching Strategy
- Cache static assets at edge (1 year)
- Cache API responses in KV (5 minutes)
- Implement stale-while-revalidate pattern
- Use ETags for conditional requests

## Security Considerations
- Sanitize all user inputs
- Implement CSRF protection
- Use Content Security Policy headers
- Enable CORS only for trusted origins
- Store sensitive data in environment variables
- Implement rate limiting at edge

## Performance Optimization
- Use React Router's prefetching
- Implement code splitting by route
- Optimize bundle size (<200KB initial)
- Use Cloudflare's edge caching
- Implement optimistic UI updates
- Lazy load non-critical components

## Debugging
- Use `wrangler tail` for production logs
- Enable React Router dev tools in development
- Use Vitest UI for debugging tests
- Chrome DevTools for performance profiling
- Playwright trace viewer for E2E debugging

## Common Tasks

### Add a new route
1. Create route file in `src/routes/`
2. Export loader/action if needed
3. Add route tests
4. Update navigation components

### Add a new todo feature
1. Update types in `src/types/`
2. Modify server logic in `src/lib/todos.server.ts`
3. Update components
4. Add tests for new functionality
5. Update E2E tests

### Database migrations
1. Create migration file in `migrations/`
2. Run locally: `wrangler d1 migrations apply DB --local`
3. Test thoroughly
4. Apply to production: `wrangler d1 migrations apply DB`

## Troubleshooting

### Build fails
- Check TypeScript errors: `npm run typecheck`
- Verify all imports are correct
- Check for circular dependencies

### Tests fail
- Run single test: `npm test -- path/to/test`
- Check test environment setup
- Verify mock implementations

### Deployment issues
- Check wrangler.toml configuration
- Verify environment variables
- Check Cloudflare Workers logs
- Ensure D1 database is properly bound

---

Remember: This is a living document. Update it as the project evolves and new patterns emerge.