# Package by Feature + Layered Architecture

## Contents
- Shared -- Application (subscribers, exceptions), Domain (base models, VOs), Infrastructure (Doctrine types, MessageBus, external clients)
- Feature contexts -- Application (HTTP, Console, DI, Security) + Domain (Entities, Handlers, Repositories with implementations)

**Dependencies:** Application -> Domain -> Infrastructure -> Nothing

With package-by-feature, each bounded context groups its layers together. However, in layered architecture, Application and Infrastructure are not inherently organized by context -- so the feature-first packaging reshapes them into context-scoped directories.

```
src/
├── Shared/
│   ├── Application/
│   │   ├── Subscriber/
│   │   │   └── ExceptionSubscriber.php
│   │   └── Exception/
│   │       └── InvalidRequestException.php
│   ├── Domain/
│   │   ├── Model/
│   │   │   └── AggregateRoot.php
│   │   ├── ValueObject/
│   │   │   ├── Money.php
│   │   │   └── Email.php
│   │   └── Exception/
│   │       └── DomainException.php
│   └── Infrastructure/
│       ├── Doctrine/
│       │   └── Type/
│       │       └── MoneyType.php
│       ├── MessageBus/
│       │   ├── CommandBusInterface.php
│       │   ├── QueryBusInterface.php
│       │   └── Adapter/
│       │       └── Symfony/
│       │           ├── SymfonyCommandBus.php
│       │           └── SymfonyQueryBus.php
│       ├── Facebook/
│       │   ├── FacebookClientInterface.php
│       │   └── FacebookClient.php
│       └── GPT/
│           ├── GPTClientInterface.php
│           └── GPTClient.php
│
├── Order/
│   ├── Application/
│   │   ├── Http/
│   │   │   ├── Action/
│   │   │   │   ├── CreateOrderAction.php
│   │   │   │   └── GetOrderAction.php
│   │   │   ├── Request/
│   │   │   │   └── CreateOrderRequest.php
│   │   │   └── Transformer/
│   │   │       └── OrderTransformer.php
│   │   ├── Console/
│   │   │   └── CleanupExpiredOrdersCommand.php
│   │   └── DependencyInjection/
│   │       ├── OrderConfiguration.php
│   │       └── OrderExtension.php
│   └── Domain/
│       ├── Model/
│       │   ├── Order.php
│       │   └── OrderId.php
│       ├── Handler/
│       │   ├── CreateOrder/
│       │   │   ├── CreateOrderCommand.php
│       │   │   └── CreateOrderCommandHandler.php
│       │   └── GetOrder/
│       │       ├── GetOrderQuery.php
│       │       └── GetOrderQueryHandler.php
│       ├── Repository/
│       │   ├── OrderRepositoryInterface.php
│       │   └── Doctrine/
│       │       └── OrderRepository.php
│       ├── Service/
│       │   └── OrderPricingService.php
│       ├── Component/
│       │   └── FeeCalculator/
│       │       ├── FeeCalculatorInterface.php
│       │       └── PercentageFeeCalculator.php
│       ├── Enum/
│       │   └── OrderStatus.php
│       ├── Event/
│       │   └── OrderCreated.php
│       └── Exception/
│           └── OrderException.php
│
├── Payment/
│   ├── Application/
│   │   ├── Http/
│   │   │   ├── Action/
│   │   │   │   └── ProcessPaymentAction.php
│   │   │   └── Request/
│   │   │       └── ProcessPaymentRequest.php
│   │   └── DependencyInjection/
│   │       ├── PaymentConfiguration.php
│   │       └── PaymentExtension.php
│   └── Domain/
│       ├── Model/
│       │   ├── Payment.php
│       │   └── PaymentId.php
│       ├── Handler/
│       │   └── ProcessPayment/
│       │       ├── ProcessPaymentCommand.php
│       │       └── ProcessPaymentCommandHandler.php
│       ├── Repository/
│       │   ├── PaymentRepositoryInterface.php
│       │   └── Doctrine/
│       │       └── PaymentRepository.php
│       └── Enum/
│           └── PaymentStatus.php
│
└── User/
    ├── Application/
    │   ├── Http/
    │   │   └── Action/
    │   │       └── RegisterUserAction.php
    │   └── Security/
    │       ├── Authenticator/
    │       │   └── UsernamePasswordAuthenticator.php
    │       ├── Voter/
    │       │   └── OrderVoter.php
    │       └── Provider/
    │           └── UserProvider.php
    └── Domain/
        ├── Model/
        │   ├── User.php
        │   └── UserId.php
        ├── Handler/
        │   └── RegisterUser/
        │       ├── RegisterUserCommand.php
        │       └── RegisterUserCommandHandler.php
        └── Repository/
            ├── UserRepositoryInterface.php
            └── Doctrine/
                └── UserRepository.php
```

Note: Feature contexts in layered architecture typically only have Application and Domain layers. Generic infrastructure (MessageBus, Facebook, GPT, shared Doctrine types) lives in `Shared/Infrastructure/` since it is domain-agnostic. A feature context only gets an Infrastructure layer if it has context-specific infrastructure (e.g., a dedicated external gateway).
