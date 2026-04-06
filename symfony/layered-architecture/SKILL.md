---
name: php-symfony-layered-architecture
description: Layered architecture guidelines for PHP Symfony applications following Clean Architecture, CQRS, and DDD. Provides structure rules, module organization, and code patterns for Application, Domain, and Infrastructure layers. Use when creating new endpoints, entities, bounded contexts, handlers, repositories, or infrastructure integrations in Symfony projects.
---

# PHP Symfony Layered Architecture

Comprehensive guide for structuring PHP Symfony applications into Application, Domain, and Infrastructure layers with CQRS and DDD patterns.

## When to Apply

Reference these guidelines when:
- Creating new API endpoints (REST or GQL)
- Adding domain entities, handlers, or repositories
- Setting up new bounded contexts
- Adding infrastructure integrations
- Configuring DependencyInjection (extensions, configurations)
- Reviewing code placement across layers

## Layers Overview

| Layer | Purpose | Depends On             |
|---|---|------------------------|
| Application | Connects user with domain. HTTP, console, config, security. | Domain, Infrastructure |
| Domain | All business logic. Organized into bounded contexts. | Infrastructure         |
| Infrastructure | Generic clients and integrations. Extractable as libraries. | Nothing                |

**Dependency rule:** Application depends on Domain and Infrastructure. Domain depends on Infrastructure interfaces (MessageBus, Storage, etc.). Infrastructure depends on nothing.

**Infrastructure abstraction rule:** Infrastructure must never know about domain-specific logic. It provides generic capabilities (e.g., send a message and receive decoded JSON). Domain-specific concerns (prompts, DTO transformation, business validation) belong to the domain context that uses the infrastructure client.

## Application Layer

| Module | Description | Reference |
|---|---|---|
| Http | Actions (ADR) or Controllers (classic) for REST/GQL | See subsection below |
| Console | CLI commands | [console-command.md](references/console-command.md) |
| DependencyInjection | Extensions, Configurations, CompilerPasses | See subsection below |
| Security | Authenticators, Token management, Voters, Providers | [security-authenticator.md](references/security-authenticator.md), [security-voter.md](references/security-voter.md) |
| Validator | Custom constraints and validators | [validator-constraint.md](references/validator-constraint.md) |
| Form | Form types | |
| Twig | Twig extensions | |
| Subscriber | System event subscribers | [event-subscriber.md](references/event-subscriber.md) |
| Exception | Application-level exceptions | [application-exception.md](references/application-exception.md) |

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

## Domain Layer

Organized into bounded contexts. Contexts can be nested (e.g., `Order/Delivery/`, `Order/Discount/`).

| Module | Description |
|---|---|
| Common / Shared | Cross-cutting models, enums, value objects, exceptions |
| {BoundedContext} | Self-contained context with own models, handlers, repositories |

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
| Handler / CQRS | Command/Query handlers, organized by use case | [cqrs-command.md](references/cqrs-command.md), [cqrs-query.md](references/cqrs-query.md) |
| Repository | Interfaces + `Doctrine/` implementations | [repository.md](references/repository.md) |
| Service | Public services for cross-context operations | |
| Component | Private business logic components | [component.md](references/component.md) |
| DAO | Data access objects returning DTOs or raw data | |
| Enum | Domain enums (camelCase cases) | [enum.md](references/enum.md) |
| Event | Domain events | |
| ValueObject | Value objects | [value-object.md](references/value-object.md) |
| Exception | Module exceptions with static factory methods | [domain-exception.md](references/domain-exception.md) |
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

### Repository

| Element | Location |
|---|---|
| `{Entity}RepositoryInterface.php` | `Repository/` (interface) |
| `Doctrine/{Entity}Repository.php` | `Repository/Doctrine/` (ORM implementation) |
| `InMemory/{Entity}Repository.php` | `Repository/InMemory/` (testing) |

### Component

Private internal services for rich business logic. Not used directly by other modules. Structure is arbitrary per component needs.

## Infrastructure Layer

Generic, reusable integrations. No domain-specific logic.

| Module | Description | Reference |
|---|---|---|
| Doctrine | Configuration, custom types | [doctrine-type.md](references/doctrine-type.md) |
| Redis | Redis client | |
| Filesystem | File system client | |
| MessageBus | Bus interfaces + adapters (e.g., Symfony Messenger) | [message-bus.md](references/message-bus.md) |
| Apple | Apple client | |
| Facebook | Facebook client | [infrastructure-client.md](references/infrastructure-client.md) |
| GPT | Generic GPT client (send message, receive JSON) | [gpt-client.md](references/gpt-client.md) |

No specific folder pattern required -- each module organizes its internals as needed.

Domain-specific usage (prompts, DTO mapping, business rules) goes into the owning domain's `Component/` or `Service/`, not Infrastructure.

## Adding New Features -- Checklists

### New API Endpoint

1. Create Request DTO in `Application/Http/Request/{Context}/`
2. Create Action or Controller in `Application/Http/Actions/{Context}/` with `#[Route]`
3. Create Command in `Domain/{Context}/Handler/{UseCaseName}/`
4. Create CommandHandler in `Domain/{Context}/Handler/{UseCaseName}/`
5. Add Response Transformer in `Application/Http/Response/Transformer/{Context}/`
6. Check if endpoint requires authorisation -- add Voter or role check if needed
7. Check if `security.yaml` access control rules need updating for the new route
8. No manual route registration needed (auto-discovery via attributes)

### New Domain Entity

1. Create entity in `Domain/{Context}/Model/`
2. Create repository interface in `Domain/{Context}/Repository/`
3. Implement Doctrine repository in `Domain/{Context}/Repository/Doctrine/`
4. Create migration: `php bin/console doctrine:migrations:diff`
5. Apply migration: `php bin/console doctrine:migrations:migrate`

### New Bounded Context

1. Create domain module under `Domain/{Context}/` with Model, Handler, Repository folders
2. Create `{Context}Configuration` in `Application/DependencyInjection/Configuration/`
3. Create `{Context}Extension` in `Application/DependencyInjection/Extension/`
4. Register both in root orchestrator classes
5. Create service config YAML in `config/services/{context}.yaml`
6. Register the new YAML in the root services configuration
7. Create Actions, Requests, Transformers in Application layer as needed

### New Configuration

1. Create `{Context}Configuration` in `Application/DependencyInjection/Configuration/`
2. Create `{Context}Extension` in `Application/DependencyInjection/Extension/`
3. Register Configuration in root `Configuration` orchestrator
4. Register Extension in root Extension orchestrator
5. Add config values at `config/{app_config_root_name}/{context}.yaml`

### New Infrastructure Integration

1. Create module folder in `Infrastructure/{Module}/`
2. Implement the integration with generic, domain-agnostic interface
3. Wire services via DependencyInjection
4. Domain-specific usage (prompts, DTO mapping, business rules) goes into the owning domain `Component/` or `Service/`
