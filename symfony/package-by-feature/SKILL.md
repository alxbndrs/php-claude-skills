---
name: php-symfony-package-by-feature
description: Package-by-feature organization for PHP Symfony applications. Top-level directories are bounded contexts (features), with architectural layers nested inside each feature. Use when deciding how to organize src/ directory structure, creating new modules, or reviewing project layout.
---

# PHP Symfony Package by Feature

Organizes code by bounded context (feature/module) first, then by architectural layer within each feature.

## When to Apply

Reference these guidelines when:
- Setting up a new Symfony project structure
- Creating a new bounded context or module
- Deciding where to place a new class
- Reviewing project organization

## Structure

```
src/
├── Order/
│   ├── Domain/
│   ├── Application/
│   └── Infrastructure/
├── Payment/
│   ├── Domain/
│   ├── Application/
│   └── Infrastructure/
├── User/
│   ├── Domain/
│   ├── Application/
│   └── Infrastructure/
└── Shared/
    ├── Domain/
    ├── Application/
    └── Infrastructure/
```

The top-level directories represent **bounded contexts** (features). Each context contains **layers** as subdirectories.

**What goes inside each layer depends on the chosen architecture** (layered, onion, hexagonal, etc.). This skill only defines the **directory organization strategy**, not the architecture itself. Combine with an architecture skill to determine what belongs in each layer.

## Everything for a Feature in One Place

All code for a bounded context lives under a single top-level directory:

```
src/Order/
├── Domain/
├── Application/
└── Infrastructure/
```

## Architecture Examples

The internal structure of each `{Context}/{Layer}/` directory depends on the architecture in use. See full directory tree examples for specific architectures:

- [Layered Architecture](references/layered-architecture.md) -- Application (HTTP, console, config, security), Domain (entities, handlers, repositories with implementations), Infrastructure (generic clients)
- [Onion Architecture](references/onion-architecture.md) -- Domain (pure rules, interfaces only), Application (handlers, services, components), Infrastructure (HTTP, persistence, DI, security, clients)

## Characteristics

**Strengths:**
- High cohesion -- all code for a feature is co-located, easy to navigate
- Adding or removing a feature is a single directory operation
- Natural fit for team ownership -- one team owns one top-level directory
- Easier to extract a context into a separate package or microservice later

**Trade-offs:**
- Layer dependency rules span multiple directory trees (Deptrac config is per-context or uses wildcards like `src/*/Domain`)
- Cross-cutting code (shared types, bus adapters) needs a `Shared/` context
- Less immediately obvious which layer a class belongs to when browsing the full `src/` tree

## Adding a New Bounded Context

1. Create `src/{Context}/Domain/`
2. Create `src/{Context}/Application/`
3. Create `src/{Context}/Infrastructure/`
4. Add service configuration in `config/services/{context}.yaml`
5. Populate each layer directory according to your architecture skill

## Nested Contexts

For complex domains, sub-contexts are top-level siblings with a naming convention (e.g., prefix or grouping directory):

```
src/
└── Order/
    ├── Discount/
    │   ├── Domain/
    │   ├── Application/
    │   └── Infrastructure/
    ├── Delivery/
    │   ├── Domain/
    │   ├── Application/
    │   └── Infrastructure/
    └── Purchase/
        ├── Domain/
        ├── Application/
        └── Infrastructure/
```

When a context is fully split into sub-contexts, the parent directory (`Order/`) acts as a grouping folder -- it does not have its own Domain/Application/Infrastructure layers. Sub-contexts follow the same structure and may depend on sibling sub-contexts' Domain but should avoid circular dependencies.

## Namespace Convention

```php
namespace App\{Context}\Domain\...;
namespace App\{Context}\Application\...;
namespace App\{Context}\Infrastructure\...;
```

Examples:

```php
namespace App\Order\Domain;
namespace App\Order\Application;
namespace App\Order\Infrastructure;
```
