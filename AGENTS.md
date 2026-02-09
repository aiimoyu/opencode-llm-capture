# Agent Guidelines for opencode-llm-capture

This is a TypeScript plugin for OpenCode that intercepts and logs LLM API interactions.

## Build Commands

```bash
# Build the plugin
npm run build

# Watch mode during development
npm run dev
```

**No test framework is configured.** This project has no tests. When adding tests, consult the team first.

## Project Structure

- Single-file plugin: `llm-capture.ts` (at project root)
- TypeScript compilation output: `dist/` (ignored by git)
- Entry point: `llm-capture.ts` (default export function returning Plugin)

## TypeScript Configuration

- Target: ES2022
- Module: ESNext
- Strict mode: enabled
- Declaration files: generated
- Type checking: strict

Always run `lsp_diagnostics` after code changes to ensure type safety.

## Code Style

### Imports
```typescript
// Type imports first
import type { Plugin } from "@opencode-ai/plugin";
// Then regular imports
import { mkdir, writeFile } from "fs/promises";
```

### Functions
- Use arrow functions for plugin exports and callbacks
- Default export is the plugin factory function
- Use `satisfies Plugin` for type assertion at end of default export

### Types
- Prefer `import type { ... }` for type-only imports
- Use `any` type assertions sparingly (e.g., `(globalThis as any)`)
- Prefer type guards and unknown over any where possible

### Naming
- Variables: `camelCase`
- Functions: `camelCase`
- Constants: `UPPER_SNAKE_CASE` (rare, mostly camelCase helpers)
- Types: `PascalCase` (from external types)

### Error Handling
- Use empty catch blocks for non-critical paths: `.catch(() => { })`
- Silent failures are acceptable for logging/debugging features
- Never throw errors that would block LLM requests

### Comments
- Chinese comments are used alongside English code
- Comments are concise and explain intent
- Comments in Chinese: `// 标记但不消费请求流` (mark but don't consume request stream)

### Headers
- Always use lowercase header keys for consistency
- Normalize: `hdrs[k.toLowerCase()] = v`
- Check multiple variations: `x-opencode-debug-session`, `X-Opencode-Debug-Session`

### Async Patterns
- Use `async/await` for all async operations
- Use `fs/promises` over callback-based APIs
- Use `try/catch` for JSON parsing and other error-prone operations

### File Operations
- Use `mkdir` with `{ recursive: true }` for directory creation
- Always ensure parent directories exist before writing
- Write JSON with `JSON.stringify(data, null, 2)` for readability
- Use helper pattern: `safeWrite` that ensures directory first

### Stream Handling
- **Never consume request streams** - snapshot only
- Clone responses before reading body: `resp.clone()`
- Detect SSE streams: `txt.startsWith("data:") || txt.includes("\ndata:")`
- Truncate large text: `txt.slice(0, 5000) + "…(truncated)"`
- Truncate SSE previews: first 200 lines only

### Global State
- Check flag before wrapping: `!(globalThis as any).__opencode_debug_fetch_wrapped`
- Use underscore prefix for internal global flags
- Bind original functions: `originalFetch?.bind(globalThis)`

### Output Files
- Filename format: `{id}-{status}-{timestamp}.json`
- ID: zero-padded 4 digits: `String(counter).padStart(4, "0")`
- Timestamp: ISO format with colons replaced: `.replace(/:/g, "-")`
- Always write `latest.json` in session directory

### Session Management
- Session dir: `~/.config/opencode/opencode-llm-capture/llm-dump/{sessionID}/` (if sessionID provided)
- Fallback: `~/.config/opencode/opencode-llm-capture/llm-dump/{YYYY-MM-DD}/` (date-based)
- Session headers: `x-opencode-debug-session`, `x-opencode-session`, `x-opencode-request`

## Critical Rules

1. **Never block LLM requests** - all plugin errors must be silent
2. **Never consume request streams** - snapshot only, don't read
3. **Always clone responses** - before reading body
4. **Header keys are lowercase** - for consistent comparison
5. **Empty catches for non-critical paths** - debug features must not fail
6. **Type safety required** - no `@ts-ignore`, no `as any` without justification