# Package by Layer + Layered Architecture

## Contents
- Application layer -- HTTP, Console, DI, Security, Validator, Subscriber (organized by module type, contexts nested within)
- Domain layer -- Entities, Handlers, Repositories (interface + Doctrine), Services, Components (organized by bounded context)
- Infrastructure layer -- Doctrine types, MessageBus, external clients (generic, no context directories)

**Dependencies:** Application -> Domain -> Infrastructure -> Nothing

```
src/
├── Application/
│   ├── Http/
│   │   ├── Action/
│   │   │   ├── Order/
│   │   │   │   ├── CreateOrderAction.php
│   │   │   │   └── GetOrderAction.php
│   │   │   ├── Payment/
│   │   │   │   └── ProcessPaymentAction.php
│   │   │   └── User/
│   │   │       └── RegisterUserAction.php
│   │   ├── Request/
│   │   │   ├── Order/
│   │   │   │   └── CreateOrderRequest.php
│   │   │   └── Payment/
│   │   │       └── ProcessPaymentRequest.php
│   │   └── Transformer/
│   │       ├── Order/
│   │       │   └── OrderTransformer.php
│   │       └── Payment/
│   │           └── PaymentTransformer.php
│   ├── Console/
│   │   └── CleanupExpiredOrdersCommand.php
│   ├── DependencyInjection/
│   │   ├── Configuration/
│   │   │   ├── OrderConfiguration.php
│   │   │   └── PaymentConfiguration.php
│   │   ├── Extension/
│   │   │   ├── OrderExtension.php
│   │   │   └── PaymentExtension.php
│   │   └── CompilerPass/
│   │       └── PaymentGatewayCompilerPass.php
│   ├── Security/
│   │   ├── Authenticator/
│   │   │   └── UsernamePasswordAuthenticator.php
│   │   ├── Voter/
│   │   │   └── OrderVoter.php
│   │   └── Provider/
│   │       └── UserProvider.php
│   ├── Validator/
│   │   └── UniqueEmail/
│   │       ├── UniqueEmail.php
│   │       └── UniqueEmailValidator.php
│   ├── Subscriber/
│   │   └── ExceptionSubscriber.php
│   └── Exception/
│       └── InvalidRequestException.php
│
├── Domain/
│   ├── Shared/
│   │   ├── Model/
│   │   │   └── AggregateRoot.php
│   │   ├── ValueObject/
│   │   │   ├── Money.php
│   │   │   └── Email.php
│   │   └── Exception/
│   │       └── DomainException.php
│   ├── Order/
│   │   ├── Model/
│   │   │   ├── Order.php
│   │   │   └── OrderId.php
│   │   ├── Handler/
│   │   │   ├── CreateOrder/
│   │   │   │   ├── CreateOrderCommand.php
│   │   │   │   └── CreateOrderCommandHandler.php
│   │   │   └── GetOrder/
│   │   │       ├── GetOrderQuery.php
│   │   │       └── GetOrderQueryHandler.php
│   │   ├── Repository/
│   │   │   ├── OrderRepositoryInterface.php
│   │   │   └── Doctrine/
│   │   │       └── OrderRepository.php
│   │   ├── Service/
│   │   │   └── OrderPricingService.php
│   │   ├── Component/
│   │   │   └── FeeCalculator/
│   │   │       ├── FeeCalculatorInterface.php
│   │   │       └── PercentageFeeCalculator.php
│   │   ├── Enum/
│   │   │   └── OrderStatus.php
│   │   ├── Event/
│   │   │   └── OrderCreated.php
│   │   └── Exception/
│   │       └── OrderException.php
│   ├── Payment/
│   │   ├── Model/
│   │   │   ├── Payment.php
│   │   │   └── PaymentId.php
│   │   ├── Handler/
│   │   │   └── ProcessPayment/
│   │   │       ├── ProcessPaymentCommand.php
│   │   │       └── ProcessPaymentCommandHandler.php
│   │   ├── Repository/
│   │   │   ├── PaymentRepositoryInterface.php
│   │   │   └── Doctrine/
│   │   │       └── PaymentRepository.php
│   │   └── Enum/
│   │       └── PaymentStatus.php
│   └── User/
│       ├── Model/
│       │   ├── User.php
│       │   └── UserId.php
│       ├── Handler/
│       │   └── RegisterUser/
│       │       ├── RegisterUserCommand.php
│       │       └── RegisterUserCommandHandler.php
│       └── Repository/
│           ├── UserRepositoryInterface.php
│           └── Doctrine/
│               └── UserRepository.php
│
└── Infrastructure/
    ├── Doctrine/
    │   └── Type/
    │       ├── OrderIdType.php
    │       └── MoneyType.php
    ├── MessageBus/
    │   ├── CommandBusInterface.php
    │   ├── QueryBusInterface.php
    │   └── Adapter/
    │       └── Symfony/
    │           ├── SymfonyCommandBus.php
    │           └── SymfonyQueryBus.php
    ├── Facebook/
    │   ├── FacebookClientInterface.php
    │   └── FacebookClient.php
    └── GPT/
        ├── GPTClientInterface.php
        └── GPTClient.php
```

Note: Application is organized by **module type** (Http, Console, Security, DI), with bounded contexts appearing as subdirectories within each module. Infrastructure contains **generic, domain-agnostic** integrations with no context-level directories. Only Domain is organized by bounded context at the top level.
