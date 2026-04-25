# @compilr-dev/factory

```
      \|/
    ╭══════════╮     ___  ___  _ __ ___  _ __ (_) |_ __
    ║'  ▐▌  ▐▌ │    / __|/ _ \| '_ ` _ \| '_ \| | | '__|
    ║          │   | (__| (_) | | | | | | |_) | | | |
    ╰─═──────═─╯    \___|\___/|_| |_| |_| .__/|_|_|_|
      \________\                        | | .dev
                                        |_| factory
```

> AI-driven application scaffolder — from declarative model to working full-stack MVP

[![npm version](https://img.shields.io/npm/v/@compilr-dev/factory.svg)](https://www.npmjs.com/package/@compilr-dev/factory)
[![License: FSL-1.1-MIT](https://img.shields.io/badge/License-FSL--1.1--MIT-blue.svg)](https://fsl.software/)

> [!WARNING]
> This package is in beta. APIs may change between minor versions.

## Overview

The factory takes an **Application Model** — a declarative description of what an app is (entities, fields, relationships, layout, features, theme) — and generates a complete, working full-stack project. It provides tools and skills that let AI agents design and scaffold applications through conversation.

## Features

- **Application Model** — declarative schema for entities, fields, relationships, layout, theme
- **Semantic Operations** — type-safe mutations (addEntity, updateField, addRelationship, etc.)
- **Optimistic Locking** — revision counter prevents lost updates
- **Auto-Inverse Relationships** — adding `belongsTo` auto-creates `hasMany` on target
- **Cascading Operations** — renaming an entity updates all relationship targets
- **Validation** — comprehensive model health checks with actionable error messages
- **Toolkit Registry** — extensible generator system (React+Node built-in)
- **5 Agent Tools** — model CRUD, validation, scaffolding, toolkit listing
- **1 Skill** — guided scaffolding workflow for AI agents
- **Full Color Palette** — auto-generates 50–950 Tailwind shades from single hex
- **Implicit Timestamps** — `createdAt`/`updatedAt` on all entity records

## Installation

```bash
npm install @compilr-dev/factory
```

Peer dependency:

```bash
npm install @compilr-dev/sdk
```

## Quick Start

```typescript
import {
  createDefaultModel,
  applyOperation,
  validateModel,
} from '@compilr-dev/factory';

// Define your application
const model = createDefaultModel({
  identity: { name: 'Restaurant Manager', description: 'Manage reservations', version: '1.0.0' },
  theme: { primaryColor: '#E65100' },
});

// Add entities through semantic operations
const updated = applyOperation(model, {
  op: 'addEntity',
  entity: {
    name: 'Reservation',
    pluralName: 'Reservations',
    icon: '📅',
    fields: [
      { name: 'date', label: 'Date', type: 'date', required: true },
      { name: 'partySize', label: 'Party Size', type: 'number', required: true },
      { name: 'status', label: 'Status', type: 'enum', required: true, enumValues: ['pending', 'confirmed', 'cancelled'] },
    ],
    views: ['card', 'list', 'detail'],
    relationships: [],
  },
});

// Validate
const result = validateModel(updated);
// { valid: true, errors: [] }
```

### Agent Tools

```typescript
import { createModelTools, createFactoryTools } from '@compilr-dev/factory';
import type { PlatformContext } from '@compilr-dev/sdk';

// 3 model tools (get, update, validate)
const modelTools = createModelTools({ context: platformContext });

// 2 factory tools (scaffold, list-toolkits)
const factoryTools = createFactoryTools({ context: platformContext });
```

## Agent Tools Reference

| Tool | Purpose |
|------|---------|
| `app_model_get` | Scoped reads of the Application Model (summary, entities, identity, etc.) |
| `app_model_update` | Semantic operations with optimistic locking |
| `app_model_validate` | Check model health and get actionable errors |
| `factory_scaffold` | Generate files from model + toolkit |
| `factory_list_toolkits` | List available toolkits with descriptions |

## Application Model

The model describes **what** an application is, independently of how it's built:

```
ApplicationModel
├── identity        — name, description, version
├── entities[]      — domain objects with fields, views, relationships
├── layout          — shell type (sidebar-header | header-only)
├── features        — boolean toggles (dashboard, auth, darkMode, etc.)
├── theme           — primaryColor (#RRGGBB)
├── techStack       — toolkit ID (e.g. 'react-node')
└── meta            — revision, timestamps, factoryVersion
```

### Semantic Operations

All model changes go through typed operations — never raw JSON editing:

| Category | Operations |
|----------|------------|
| **Entity** | addEntity, updateEntity, removeEntity, renameEntity, reorderEntities |
| **Field** | addField, updateField, removeField, renameField |
| **Relationship** | addRelationship, removeRelationship (with auto-inverse) |
| **Section** | updateIdentity, updateLayout, updateFeatures, updateTheme, updateTechStack |

Every operation follows: **READ → CHECK → APPLY → VALIDATE → WRITE**.

### Key Design Choices

- **Name-based addressing** — `entity: "Reservation"`, not `entities[2]`
- **Optimistic locking** — revision counter prevents lost updates
- **Auto-inverse relationships** — adding `belongsTo` auto-adds `hasMany` on target
- **Cascading renames** — renaming an entity updates all FK references
- **Scoped reads** — `app_model_get({ scope: "summary" })` for lightweight overview

## Toolkits

### React + Node (`react-node`)

Generates a complete full-stack application:

| Category | Generated Files |
|----------|----------------|
| **Config** | package.json, vite.config.ts, tailwind.config.js, tsconfig.json, postcss.config.js |
| **Server** | Express API with CRUD routes, in-memory data stores, seed data |
| **Pages** | List (card + table views), Detail, Dashboard |
| **Components** | Cards, Forms, SearchBar, ViewToggle, FilterBar, DarkModeToggle |
| **Routing** | React Router with nested layouts |
| **Shell** | Sidebar + header layout, responsive navigation |
| **Static** | index.html, global CSS, favicon |

**Highlights:**
- `@tailwindcss/forms` plugin included
- Full primary color palette (50–950 shades) auto-generated from theme
- Implicit `createdAt`/`updatedAt` timestamps on all entities
- Enum fields get filter bars on list pages
- `belongsTo` relationships get populated API responses and form dropdowns
- `hasMany` relationships shown as linked lists on detail pages
- Deterministic seed data (8 items per entity with realistic values)

## Development

```bash
npm run build          # Compile TypeScript
npm run lint           # ESLint (strict, 0 errors required)
npm run format:check   # Prettier check
npm test -- --run      # Run 290 tests
npm run typecheck      # Type-check without emitting
```

## Requirements

- **Node.js** 18 or higher
- **@compilr-dev/sdk** ^0.1.12 (peer dependency)

## Related Packages

| Package | Description |
|---------|-------------|
| [@compilr-dev/sdk](https://www.npmjs.com/package/@compilr-dev/sdk) | Universal agent runtime |
| [@compilr-dev/agents](https://www.npmjs.com/package/@compilr-dev/agents) | Multi-LLM agent library |
| [@compilr-dev/agents-coding](https://www.npmjs.com/package/@compilr-dev/agents-coding) | Coding-specific tools and language analysis |
| [@compilr-dev/cli](https://www.npmjs.com/package/@compilr-dev/cli) | AI-powered coding assistant for your terminal |

## Links

- [Website](https://compilr.dev)
- [npm Package](https://www.npmjs.com/package/@compilr-dev/factory)
- [Report Issues](https://github.com/compilr-dev/factory/issues)

## License

[FSL-1.1-MIT](https://fsl.software/) - See [LICENSE](LICENSE) for details. Converts to MIT after 2 years per version.

---

<p align="center">
  <strong>Built with care by <a href="https://compilr.dev">compilr.dev</a></strong>
</p>
