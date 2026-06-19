# sanity-foundry-constants

[![npm version](https://img.shields.io/npm/v/@liiift-studio/sanity-foundry-constants.svg)](https://www.npmjs.com/package/@liiift-studio/sanity-foundry-constants)
[![license](https://img.shields.io/npm/l/@liiift-studio/sanity-foundry-constants.svg)](./package.json)

Shared, **environment-driven** constants for Liiift foundry Sanity Studios. One small package so every studio (Darden, TDF, Positype, Sorkin, MCKL…) reads the same script list and discount-requirement types from environment variables instead of hard-coding them per project. Also ships a tiny `HtmlDescription` passthrough component.

The exported lists are derived **at import time** from environment variables — set them in each studio's environment, and every consumer sees the same values.

## Install

```bash
npm install @liiift-studio/sanity-foundry-constants
```

Import specifier: `@liiift-studio/sanity-foundry-constants` (dual ESM/CJS build; `react >=18` peer dependency).

## Environment variables

Both lists are empty (`[]`) until you set the corresponding env var. Values are **comma-separated** and trimmed.

| Variable | Drives | Example |
|---|---|---|
| `SANITY_STUDIO_SCRIPTS` | `SCRIPTS`, `SCRIPTS_OBJECT` | `latin,greek,cyrillic` |
| `SANITY_STUDIO_DISCOUNT_REQ_TYPES` | `DISCOUNT_REQUIREMENT_TYPES`, `DISCOUNT_REQUIREMENT_TYPES_OBJECT` | `percentage,fixed,bundle` |

> Sanity Studio exposes only env vars prefixed with `SANITY_STUDIO_` to the studio bundle, which is why both variables use that prefix.

## Exported values

| Export | Type | What it is |
|---|---|---|
| `SCRIPTS` | `string[]` | Script variants from `SANITY_STUDIO_SCRIPTS`, e.g. `['latin', 'greek']`. |
| `SCRIPTS_OBJECT` | `{ title: string; value: string }[]` | `SCRIPTS` mapped to Sanity select options (title is capitalized), e.g. `[{ title: 'Latin', value: 'latin' }]`. |
| `DISCOUNT_REQUIREMENT_TYPES` | `string[]` | Discount requirement types from `SANITY_STUDIO_DISCOUNT_REQ_TYPES`. |
| `DISCOUNT_REQUIREMENT_TYPES_OBJECT` | `{ title: string; value: string }[]` | The above mapped to Sanity select options. |
| `HtmlDescription` | `React` component | Passthrough that renders its `children` as-is (returns `''` when empty). |

> Note: `SCRIPTS_OBJECT` and `DISCOUNT_REQUIREMENT_TYPES_OBJECT` are **arrays of `{ title, value }`** — not string-keyed maps. They are shaped to drop straight into a Sanity field's `options.list`. The `title` only capitalizes the first letter of `value` (so `cjk` becomes `Cjk`, not `CJK`) — override the option list manually if you need exact display casing for acronyms.

## Usage

```typescript
import {
	SCRIPTS,
	SCRIPTS_OBJECT,
	DISCOUNT_REQUIREMENT_TYPES,
	DISCOUNT_REQUIREMENT_TYPES_OBJECT,
} from '@liiift-studio/sanity-foundry-constants'

// With SANITY_STUDIO_SCRIPTS="latin,greek":
SCRIPTS // => ['latin', 'greek']
SCRIPTS_OBJECT // => [{ title: 'Latin', value: 'latin' }, { title: 'Greek', value: 'greek' }]

// With SANITY_STUDIO_DISCOUNT_REQ_TYPES="percentage,fixed":
DISCOUNT_REQUIREMENT_TYPES // => ['percentage', 'fixed']
DISCOUNT_REQUIREMENT_TYPES_OBJECT // => [{ title: 'Percentage', value: 'percentage' }, ...]
```

### In a Sanity schema

The `*_OBJECT` arrays plug directly into a select field's `options.list`:

```typescript
import { defineField } from 'sanity'
import { SCRIPTS_OBJECT } from '@liiift-studio/sanity-foundry-constants'

defineField({
	name: 'script',
	title: 'Script',
	type: 'string',
	options: { list: SCRIPTS_OBJECT },
})
```

### HtmlDescription

`HtmlDescription` renders whatever you pass as `children` (used for fields whose value is already a rendered node). It does **not** parse an HTML string prop.

```tsx
import React from 'react'
import { HtmlDescription } from '@liiift-studio/sanity-foundry-constants'

export const MyComponent = () => (
	<HtmlDescription>
		<p>Some <strong>description</strong> content</p>
	</HtmlDescription>
)
```

## Peer Dependencies

| Package | Version |
|---|---|
| `react` | `>=18` |

## License

MIT © Liiift Studio
