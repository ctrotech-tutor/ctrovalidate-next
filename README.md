# ctrovalidate-next

**Server-side validation for Next.js Server Actions.**

`ctrovalidate-next` provides `validateAction` and `formDataToValues` utilities for validating `FormData` against `ctrovalidate-core` schemas in Next.js Server Actions. Includes multi-value field handling (checkboxes, multi-select).

[![npm version](https://img.shields.io/npm/v/ctrovalidate-next.svg)](https://www.npmjs.com/package/ctrovalidate-next)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

---

## Installation

```bash
npm install ctrovalidate-next ctrovalidate-core
```

**Requirements:** Next.js >=13.4.0

---

## Quick Start

```typescript
// app/actions.ts
'use server';

import { validateAction } from 'ctrovalidate-next';

interface SignupForm { email: string; password: string; }

export async function signup(prevState: unknown, formData: FormData) {
  const { isValid, errors, values } = await validateAction<SignupForm>(
    formData,
    { email: 'required|email', password: 'required|minLength:8' }
  );

  if (!isValid) return { success: false, errors };

  await db.createUser(values);
  return { success: true };
}
```

---

## API

### `validateAction<T>(formData, schema, options?)`

| Param | Type | Required |
|-------|------|----------|
| `formData` | FormData | Yes |
| `schema` | ValidationSchema | Yes |
| `options` | ValidateActionOptions | No |

Returns `Promise<{ isValid: boolean; errors: Partial<Record<keyof T, string>>; values: T }>`.

### `formDataToValues<T>(formData)`

Converts `FormData` to a typed plain object. Multiple values for the same key are collected into arrays.

### `ValidateActionOptions`

```typescript
{
  customRules?: Record<string, RuleLogic | AsyncRuleLogic>;
  aliases?: Record<string, SchemaRule>;
  messages?: Record<string, string>;  // Custom error messages
  locale?: string;                    // i18n locale
}
```

---

## Client + Server Pattern

Pair with `ctrovalidate-react` for instant client-side feedback:

```tsx
'use client';

import { useCtrovalidate } from 'ctrovalidate-react';
import { useActionState } from 'react';

const { values, errors, handleChange, handleBlur } = useCtrovalidate({
  initialValues: { email: '', password: '' },
  schema: { email: 'required|email', password: 'required|minLength:8' },
});

const [state, action] = useActionState(signup, { success: false, errors: {} });

// Show client errors immediately, server errors after submission
{errors.email && <p>{errors.email}</p>}
{state.errors?.email && <p>{state.errors.email}</p>}
```

---

## Related Packages

- **[ctrovalidate-core](https://www.npmjs.com/package/ctrovalidate-core)** — Validation engine
- **[ctrovalidate-react](https://www.npmjs.com/package/ctrovalidate-react)** — React hook (client-side)
- **[ctrovalidate-browser](https://www.npmjs.com/package/ctrovalidate-browser)** — Vanilla JS DOM integration
- **[ctrovalidate-vue](https://www.npmjs.com/package/ctrovalidate-vue)** — Vue composable
- **[ctrovalidate-svelte](https://www.npmjs.com/package/ctrovalidate-svelte)** — Svelte stores

---

## License

MIT © [Ctrotech](https://github.com/ctrotech-tutor)

Full documentation: https://ctrovalidate.vercel.app
