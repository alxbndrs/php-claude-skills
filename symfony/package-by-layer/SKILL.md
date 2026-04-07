---
name: php-symfony-package-by-layer
description: Package-by-layer organization for PHP Symfony applications. Top-level directories are architectural layers (Domain, Application, Infrastructure), with bounded contexts nested inside each layer. Use when deciding how to organize src/ directory structure, creating new modules, or reviewing project layout.
---

# PHP Symfony Package by Layer

Organizes code by architectural layer first, then by bounded context (feature/module) within each layer.

## When to Apply

Reference these guidelines when:
- Setting up a new Symfony project structure
- Creating a new bounded context or module
- Deciding where to place a new class
- Reviewing project organization

## Structure

```
src/
├── Domain/
│   ├── Order/
│   ├── Payment/
│   ├── User/
│   └── Shared/
├── Application/
└── Infrastructure/
```

The top-level directories represent **layers**. Each layer contains **bounded contexts** (packages) as subdirectories.

**What goes inside each layer depends on the chosen architecture** (layered, onion, hexagonal, etc.). This skill only defines the **directory organization strategy**, not the architecture itself. Combine with an architecture skill to determine what belongs in each layer.

## Bounded Context Across Layers

Each bounded context is split across all three layers. The same context name appears in each layer directory:

```
src/Domain/Order/
src/Application/../Order/
src/Infrastructure/../Order/
```

## Architecture Examples

The internal structure of each `{Layer}/../{Context}/` directory depends on the architecture in use. See full directory tree examples for specific architectures:

- [Layered Architecture](references/layered-architecture.md) -- Application (HTTP, console, config, security), Domain (entities, handlers, repositories with implementations), Infrastructure (generic clients)
- [Onion Architecture](references/onion-architecture.md) -- Domain (pure rules, interfaces only), Application (handlers, services, components), Infrastructure (HTTP, persistence, DI, security, clients)

## Characteristics

**Strengths:**
- Clear separation of concerns -- easy to enforce layer dependency rules
- Natural fit for dependency analysis tools (e.g., Deptrac) -- one rule per layer boundary
- Familiar to teams experienced with traditional layered/onion/hexagonal architectures

**Trade-offs:**
- Related code for one feature is spread across three directories
- Adding a new feature requires creating directories in multiple layer trees
- Navigating between related classes for a feature requires jumping between distant directories

## Adding a New Bounded Context

1. Create `src/Domain/{Context}/`
2. Create `src/Application/../{Context}/` where needed according to architecture.
3. Create `src/Infrastructure/../{Context}/`  where needed according to architecture.
4. Add service configuration in `config/services/{context}.yaml` if needed.
5. Populate each layer directory according to your architecture skill.

## Namespace Convention

* might differ for different architectures * 

```php
namespace App\Domain\{Context}\...;
namespace App\Application\{Context}\...;
namespace App\Infrastructure\{Context}\...;
```

Examples:

```php
namespace App\Domain\Order;
namespace App\Application\Order;
namespace App\Infrastructure\Order;
```
