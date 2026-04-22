---
name: TypeScript
description: >
  TypeScript with strict mode: const enums, discriminated unions, generics, and zero-compromise type safety.
  Trigger: Building type-safe applications with TypeScript in strict mode.
license: MIT
metadata:
  author: Henry Carmenate
  version: "1.0"
  applies-to:
    - claude-code
    - cursor
    - codex
---

## When to Use
Use this skill when:
- Building TypeScript projects with strict mode enabled
- Designing type-safe APIs with discriminated unions
- Creating reusable generic utilities and functions
- Using advanced types like Partial, Pick, Omit, and Record
- Ensuring compile-time safety before runtime

## Critical Patterns

### Pattern 1: Strict Configuration and No Implicit Any
Enable all strict flags in tsconfig.json to catch errors at compile time.

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    "alwaysStrict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    },
    "target": "ES2020",
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true
  }
}
```

### Pattern 2: Discriminated Unions for Type Narrowing
Use discriminated unions to handle multiple result types safely.

```typescript
// Result type that represents success or failure
type Result<T> = 
  | { status: 'ok'; data: T; timestamp: Date }
  | { status: 'error'; error: Error; code: string };

// HTTP response type
type ApiResponse<T> =
  | { type: 'success'; payload: T; statusCode: 200 | 201 }
  | { type: 'validation_error'; errors: Record<string, string>; statusCode: 422 }
  | { type: 'not_found'; message: string; statusCode: 404 }
  | { type: 'server_error'; message: string; statusCode: 500 };

// Type guard function
function isSuccess<T>(response: ApiResponse<T>): response is ApiResponse<T> & { type: 'success' } {
  return response.type === 'success';
}

// Usage with type narrowing
async function fetchUser(userId: number): Promise<ApiResponse<User>> {
  const response = await fetch(`/api/users/${userId}`);
  const data = await response.json();
  
  if (response.status === 200) {
    return { type: 'success', payload: data as User, statusCode: 200 };
  } else if (response.status === 404) {
    return { type: 'not_found', message: 'User not found', statusCode: 404 };
  } else {
    return { type: 'server_error', message: 'Internal server error', statusCode: 500 };
  }
}

// Exhaustive handling
const response = await fetchUser(1);
if (isSuccess(response)) {
  console.log(response.payload.name); // TypeScript knows payload exists
} else if (response.type === 'not_found') {
  console.log(response.message); // TypeScript knows message exists
} else {
  const _exhaustive: never = response;
}
```

### Pattern 3: Const Enums (Not Regular Enums)
Use const enums to avoid runtime overhead and get better tree-shaking.

```typescript
// ❌ Regular enum (creates runtime object)
enum Status {
  Pending = 'pending',
  Active = 'active',
  Inactive = 'inactive'
}

// ✅ Const enum (inlined, no runtime code)
const enum UserRole {
  Admin = 'admin',
  Moderator = 'moderator',
  User = 'user'
}

// Const enum usage
type User = {
  id: number;
  name: string;
  role: UserRole;
};

function hasAdminAccess(user: User): boolean {
  return user.role === UserRole.Admin;
}

// Compiled to:
// function hasAdminAccess(user) {
//   return user.role === "admin";
// }
```

### Pattern 4: Generic Constraints and Utility Types
Use generics with constraints to create reusable, type-safe utilities.

```typescript
// Generic constraint: T must have an 'id' property
type Entity = { id: number };

function getEntityId<T extends Entity>(entity: T): number {
  return entity.id;
}

// Utility types for common operations
type Nullable<T> = T | null;
type Readonly<T> = { readonly [K in keyof T]: T[K] };

// Pick: extract specific properties
type UserPreview = Pick<User, 'id' | 'name'>;

// Omit: exclude specific properties
type UserWithoutPassword = Omit<User, 'password'>;

// Partial: make all properties optional
type UserUpdate = Partial<User>;

// Required: make all properties required
type UserStrict = Required<User>;

// Record: create object with specific keys
type Permissions = Record<UserRole, string[]>;

const permissions: Permissions = {
  [UserRole.Admin]: ['read', 'write', 'delete'],
  [UserRole.User]: ['read'],
  [UserRole.Moderator]: ['read', 'write']
};

// Readonly: prevent mutations
type ReadonlyUser = Readonly<User>;
const user: ReadonlyUser = { id: 1, name: 'John', role: UserRole.User };
// user.name = 'Jane'; // TS Error: cannot assign to readonly property
```

### Pattern 5: Function Overloads (Unions Preferred)
Prefer union types over function overloads for cleaner code.

```typescript
// ❌ Function overloads (verbose)
function process(input: string): string;
function process(input: number): number;
function process(input: string | number): string | number {
  if (typeof input === 'string') {
    return input.toUpperCase();
  }
  return input * 2;
}

// ✅ Union type (cleaner)
function transform(input: string | number): string | number {
  return typeof input === 'string' ? input.toUpperCase() : input * 2;
}

// Even better: separate functions or use generics
function transformString(input: string): string {
  return input.toUpperCase();
}

function transformNumber(input: number): number {
  return input * 2;
}

// Or use a generic factory function
function createTransformer<T extends string | number>(
  input: T
): T extends string ? string : number {
  return (typeof input === 'string' ? input.toUpperCase() : input * 2) as any;
}
```

## Folder Structure
```
src/
├── types/
│   ├── index.ts                    # All type exports
│   ├── user.ts
│   ├── post.ts
│   └── api.ts
├── utils/
│   ├── validation.ts
│   ├── formatting.ts
│   └── helpers.ts
├── services/
│   ├── user.service.ts
│   └── post.service.ts
├── api/
│   ├── client.ts
│   └── endpoints.ts
├── hooks/
│   ├── useUser.ts
│   └── usePosts.ts
├── components/
│   └── ... (if React/Vue)
├── config.ts
└── main.ts

tests/
├── unit/
│   └── utils.test.ts
└── integration/
    └── api.test.ts

tsconfig.json
tsconfig.strict.json                # Even stricter config
```

## Key Principles
- **Strict mode mandatory**: Enable all strict flags. Never use `any` without `// @ts-expect-error`.
- **Discriminated unions for control flow**: Use unions to encode success/error states, not boolean flags.
- **Const enums over regular enums**: Enums add runtime code; const enums get inlined.
- **Generics with constraints**: Use `<T extends SomeType>` to write reusable, type-safe utilities.
- **Utility types**: Use Pick, Omit, Partial, Required, Record, Readonly to derive types from existing ones.
- **No implicit any**: Every parameter and return type must be explicit.
- **Avoid function overloads**: Use union types or separate functions instead.
- **Type narrowing**: Use `if (typeof x === 'string')` or custom type guards for accurate types.

## Commands & Setup
```bash
# Initialize TypeScript
npm init -y
npm install --save-dev typescript @types/node

# Create tsconfig.json
npx tsc --init --strict

# Type check
npx tsc --noEmit

# Build
npx tsc

# Watch mode
npx tsc --watch
```
