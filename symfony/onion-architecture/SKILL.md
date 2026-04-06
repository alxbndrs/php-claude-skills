---
name: php-symfony-onion-architecture
description: Onion architecture guidelines for PHP Symfony applications following CQRS and DDD. Domain at the center (entities, value objects, interfaces only), Application layer for use-case orchestration (handlers, services), Infrastructure as the outermost layer (HTTP, persistence, external integrations). Use when creating new endpoints, entities, bounded contexts, handlers, repositories, or infrastructure integrations in Symfony projects.
---

# PHP Symfony Onion Architecture

Comprehensive guide for structuring PHP Symfony applications using the Onion Architecture pattern with CQRS and DDD. Domain is the innermost layer (pure business rules), Application orchestrates use cases, and Infrastructure handles all external concerns.

## When to Apply

Reference these guidelines when:
- Creating new API endpoints (REST or GQL)
- Adding domain entities, handlers, or repositories
- Setting up new bounded contexts
- Adding infrastructure integrations (persistence, external APIs)
- Configuring DependencyInjection (extensions, configurations)
- Reviewing code placement across layers

## Layers Overview

```
┌─────────────────────────────────────────────┐
│  Infrastructure (outermost)                 │
│  HTTP, Console, Doctrine, External clients  │
│  ┌─────────────────────────────────────┐    │
│  │  Application                        │    │
│  │  Handlers, Services, Components     │    │
│  │  ┌─────────────────────────────┐    │    │
│  │  │  Domain (innermost)         │    │    │
│  │  │  Entities, VOs, Interfaces  │    │    │
│  │  └─────────────────────────────┘    │    │
│  └─────────────────────────────────────┘    │
└─────────────────────────────────────────────┘
```

| Layer | Purpose | Depends On |
|---|---|---|
| Domain | Pure business rules: entities, value objects, repository interfaces, enums, events | Nothing (pure PHP) |
| Application | Use-case orchestration: CQRS handlers, services, components | Domain |
| Infrastructure | External concerns: HTTP, persistence, DI, security, third-party clients | Application, Domain |

**Dependency rule:** Dependencies point inward only. Infrastructure depends on Application and Domain. Application depends on Domain. Domain depends on nothing.

**Domain purity rule:** Domain contains only business rules and interfaces. No framework dependencies, no implementation details, no use-case orchestration. Repository interfaces are defined in Domain; implementations live in Infrastructure.

## Code Conventions

- Classes without mutable state must be `final readonly` by default
- Enum cases use `camelCase` (e.g., `pending`, `confirmed`, not `PENDING`)
- Entities and Command/Query classes are NOT `readonly` (they have mutable state)

## Domain Layer

The innermost layer. Pure business rules only. No framework dependencies.

Organized into bounded contexts. Contexts can be nested (e.g., `Order/Delivery/`, `Order/Discount/`).

| Module | Description |
|---|---|
| Common / Shared | Cross-cutting models, enums, value objects, exceptions |
| {BoundedContext} | Self-contained context with own models, interfaces, and domain rules |

### Shared Context Structure

```
Shared/
├── Model/          # AggregateRoot
├── Enum/           # Currency, Status
├── ValueObject/    # Money, Email, DateRange
└── Exception/      # DomainException
```

### Module (Bounded Context)

| Module | Description | Reference |
|---|---|---|
| Model / Entity | Entities and identity value objects | [entity.md](references/entity.md) |
| Repository | Repository interfaces only (no implementations) | [repository-interface.md](references/repository-interface.md) |
| ReadRepository | Read-only repository interfaces only | |
| DAO | Data access object interfaces only | |
| DTO | Data transfer objects | |
| Enum | Domain enums (camelCase cases) | [enum.md](references/enum.md) |
| Event | Domain events | |
| ValueObject | Value objects | [value-object.md](references/value-object.md) |
| Exception | Module exceptions with static factory methods | [domain-exception.md](references/domain-exception.md) |

## Application Layer

Use-case orchestration. Contains CQRS handlers, services, and business logic components. Organized into bounded contexts matching the Domain layer.

| Module | Description |
|---|---|
| Common / Shared | Cross-cutting application services and exceptions |
| {BoundedContext} | Use-case handlers and services for a domain context |

### Context (Bounded Context)

Contexts can contain sub-contexts as nested components with arbitrary internal structure. Common top-level modules:

| Module | Description | Reference |
|---|---|---|
| Handler / CQRS | Command/Query handlers, organized by use case | [cqrs-command.md](references/cqrs-command.md), [cqrs-query.md](references/cqrs-query.md) |
| Service | Public services for cross-context operations | |
| Component | Private business logic components (arbitrary internal structure) | [component.md](references/component.md) |
| Exception | Application-level exceptions with static factory methods | |
| Subscriber / Listener | Domain event handlers | |

### Handler (CQRS)

Each use case gets its own folder:

```
Handler/
  {UseCaseName}/
    {UseCaseName}Command.php           # Immutable DTO with getResult()/setResult()
    {UseCaseName}CommandHandler.php     # #[AsMessageHandler], __invoke(), final readonly
    Result/                            # Optional result DTOs
```

Queries follow the same pattern with `*Query` / `*QueryHandler`.

**Dispatch:**
```php
$this->commandBus->dispatch($command = new CreateOrderCommand(...));
$result = $command->getResult();
```

### Component

Private internal services for rich business logic within a context. Not used directly by other contexts. Structure is arbitrary per component needs. Useful for encapsulating complex algorithms, calculators, rule engines, or integration adapters with domain-specific logic.

### Context Structure Example

```
Order/
├── Discount/
│   ├── Calculator/
│   ├── ApplicabilityRule/
│   └── Handler/
│       └── ApplyDiscount/
│           ├── ApplyDiscountCommand.php
│           └── ApplyDiscountCommandHandler.php
├── Delivery/
│   └── ...
├── Component/
│   └── FeeCalculator/
│       ├── FeeCalculatorInterface.php
│       ├── PercentageFeeCalculator.php
│       └── CompositeFeeCalculator.php
├── Handler/
│   ├── CreateOrder/
│   │   ├── CreateOrderCommand.php
│   │   └── CreateOrderCommandHandler.php
│   └── GetOrder/
│       ├── GetOrderQuery.php
│       └── GetOrderQueryHandler.php
└── Service/
    └── OrderPricingService.php
```

## Infrastructure Layer

The outermost layer. All external concerns: HTTP transport, persistence, DI configuration, security, and third-party integrations.

| Module | Description | Reference |
|---|---|---|
| Http | Actions (ADR) or Controllers (classic) for REST/GQL | See subsection below |
| Console | CLI commands | [console-command.md](references/console-command.md) |
| DependencyInjection | Extensions, Configurations, CompilerPasses | See subsection below |
| Security | Authenticators, Token management, Voters, Providers | [security-authenticator.md](references/security-authenticator.md), [security-voter.md](references/security-voter.md) |
| Validator | Custom constraints and validators | [validator-constraint.md](references/validator-constraint.md) |
| Form | Form types | |
| Twig | Twig extensions | |
| Subscriber | System event subscribers (kernel, request lifecycle) | [event-subscriber.md](references/event-subscriber.md) |
| Exception | Infrastructure-level exceptions | [application-exception.md](references/application-exception.md) |
| Doctrine | Configuration, custom types, repository implementations | [doctrine-type.md](references/doctrine-type.md), [repository-doctrine.md](references/repository-doctrine.md) |
| Redis | Redis client | |
| Filesystem | File system client | |
| MessageBus | Bus interfaces + adapters (e.g., Symfony Messenger) | [message-bus.md](references/message-bus.md) |
| Apple | Apple client | |
| Facebook | Facebook client | [infrastructure-client.md](references/infrastructure-client.md) |
| GPT | Generic GPT client (send message, receive JSON) | [gpt-client.md](references/gpt-client.md) |

No specific folder pattern required for external client modules -- each organizes its internals as needed.

Domain-specific usage of infrastructure clients (prompts, DTO mapping, business rules) goes into the owning Application context's `Component/` or `Service/`, not Infrastructure.

### Http (REST)

| Module | Description | Reference |
|---|---|---|
| Action / Controller | ADR (one class per endpoint) or classic (multiple endpoints per class) | [rest-action.md](references/rest-action.md), [rest-controller.md](references/rest-controller.md) |
| Request | Request DTOs with validation attributes | [request-dto.md](references/request-dto.md) |
| Response/Transformer | Domain model to API response transformation | [response-transformer.md](references/response-transformer.md) |

### Http (GQL)

| Module | Description | Reference |
|---|---|---|
| Action/Controller | GraphQL endpoint handlers | [graphql.md](references/graphql.md) |
| Mutation | Input type configurations | [graphql.md](references/graphql.md) |
| Query | Query type configurations | [graphql.md](references/graphql.md) |

### DependencyInjection

Each bounded context gets a paired Extension + Configuration class. A root orchestrator loads them all.

| Module | Description | Reference |
|---|---|---|
| Extension | Loads config values and registers services | [di-extension.md](references/di-extension.md) |
| Configuration | Configuration tree definitions | [di-configuration.md](references/di-configuration.md) |
| CompilerPass | Advanced container manipulation (tag collection, service decoration) | [di-compiler-pass.md](references/di-compiler-pass.md) |

**Configuration YAML files** are placed in `config/{app_config_root_name}/{context}.yaml` -- at the same level as `packages/`, `services/`, etc. The `{app_config_root_name}` is the root name registered in the Kernel.

**Service YAML files** are placed in `config/services/{context}.yaml` or as a folder `config/services/{context}/` containing multiple files (e.g., `repository.yaml`, `handler.yaml`). Both approaches are valid.

### Security

```
Security/
├── Authenticator/          # AbstractOAuth2, Google, Facebook, UsernamePassword, RefreshToken
├── Token/                  # TokenGeneratorInterface, JwtTokenGenerator, RefreshToken, Manager
│   └── Exception/          # TokenExpiredException
├── Authorisation/
│   └── Voter/              # OrderVoter, AdminVoter
└── Provider/               # UserIdBasedUserProvider, UsernameBasedUserProvider
```

### Repository Implementations

Repository interfaces are defined in Domain. Implementations live in Infrastructure:

| Element | Location |
|---|---|
| `{Entity}RepositoryInterface.php` | `Domain/{Context}/Repository/` |
| `{Entity}Repository.php` | `Infrastructure/Doctrine/Repository/{Context}/` |
| `InMemory{Entity}Repository.php` | `Infrastructure/InMemory/Repository/{Context}/` (testing) |

## Adding New Features -- Checklists

### New API Endpoint

1. Create Request DTO in `Infrastructure/Http/Request/{Context}/`
2. Create Action or Controller in `Infrastructure/Http/Actions/{Context}/` with `#[Route]`
3. Create Command in `Application/{Context}/Handler/{UseCaseName}/`
4. Create CommandHandler in `Application/{Context}/Handler/{UseCaseName}/`
5. Add Response Transformer in `Infrastructure/Http/Response/Transformer/{Context}/`
6. Check if endpoint requires authorisation -- add Voter or role check if needed
7. Check if `security.yaml` access control rules need updating for the new route
8. No manual route registration needed (auto-discovery via attributes)

### New Domain Entity

1. Create entity in `Domain/{Context}/Model/`
2. Create repository interface in `Domain/{Context}/Repository/`
3. Implement Doctrine repository in `Infrastructure/Doctrine/Repository/{Context}/`
4. Create migration: `php bin/console doctrine:migrations:diff`
5. Apply migration: `php bin/console doctrine:migrations:migrate`

### New Bounded Context

1. Create Domain module under `Domain/{Context}/` with Model, Repository (interfaces), Enum folders
2. Create Application module under `Application/{Context}/` with Handler, Service folders
3. Create `{Context}Configuration` in `Infrastructure/DependencyInjection/Configuration/`
4. Create `{Context}Extension` in `Infrastructure/DependencyInjection/Extension/`
5. Register both in root orchestrator classes
6. Create service config YAML in `config/services/{context}.yaml`
7. Register the new YAML in the root services configuration
8. Create Actions, Requests, Transformers in `Infrastructure/Http/` as needed

### New Configuration

1. Create `{Context}Configuration` in `Infrastructure/DependencyInjection/Configuration/`
2. Create `{Context}Extension` in `Infrastructure/DependencyInjection/Extension/`
3. Register Configuration in root `Configuration` orchestrator
4. Register Extension in root Extension orchestrator
5. Add config values at `config/{app_config_root_name}/{context}.yaml`

### New Infrastructure Integration

1. Create module folder in `Infrastructure/{Module}/`
2. Implement the integration with generic, domain-agnostic interface
3. Wire services via DependencyInjection
4. Domain-specific usage (prompts, DTO mapping, business rules) goes into the owning Application context's `Component/` or `Service/`
