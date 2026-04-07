# Package by Layer + Onion Architecture

## Contents
- Domain layer -- Entities, Value Objects, Repository interfaces, Enums, Events, Exceptions (pure PHP, no implementations)
- Application layer -- CQRS Handlers, Services, Components (organized by bounded context)
- Infrastructure layer -- HTTP, Console, Doctrine (types + repository implementations), DI, Security, MessageBus, external clients (organized by bounded context)

**Dependencies:** Infrastructure -> Application -> Domain -> Nothing

```
src/
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
│   │   ├── Repository/
│   │   │   └── OrderRepositoryInterface.php
│   │   ├── Enum/
│   │   │   └── OrderStatus.php
│   │   ├── Event/
│   │   │   └── OrderCreated.php
│   │   ├── ValueObject/
│   │   │   └── OrderTotal.php
│   │   └── Exception/
│   │       └── OrderException.php
│   ├── Payment/
│   │   ├── Model/
│   │   │   ├── Payment.php
│   │   │   └── PaymentId.php
│   │   ├── Repository/
│   │   │   └── PaymentRepositoryInterface.php
│   │   └── Enum/
│   │       └── PaymentStatus.php
│   └── User/
│       ├── Model/
│       │   ├── User.php
│       │   └── UserId.php
│       └── Repository/
│           └── UserRepositoryInterface.php
│
├── Application/
│   ├── Shared/
│   │   └── Exception/
│   │       └── ApplicationException.php
│   ├── Order/
│   │   ├── Handler/
│   │   │   ├── CreateOrder/
│   │   │   │   ├── CreateOrderCommand.php
│   │   │   │   └── CreateOrderCommandHandler.php
│   │   │   └── GetOrder/
│   │   │       ├── GetOrderQuery.php
│   │   │       └── GetOrderQueryHandler.php
│   │   ├── Service/
│   │   │   └── OrderPricingService.php
│   │   └── Component/
│   │       └── FeeCalculator/
│   │           ├── FeeCalculatorInterface.php
│   │           └── PercentageFeeCalculator.php
│   ├── Payment/
│   │   └── Handler/
│   │       └── ProcessPayment/
│   │           ├── ProcessPaymentCommand.php
│   │           └── ProcessPaymentCommandHandler.php
│   └── User/
│       └── Handler/
│           └── RegisterUser/
│               ├── RegisterUserCommand.php
│               └── RegisterUserCommandHandler.php
│
└── Infrastructure/
    ├── Shared/
    │   ├── Doctrine/
    │   │   └── Type/
    │   │       └── MoneyType.php
    │   ├── MessageBus/
    │   │   ├── CommandBusInterface.php
    │   │   ├── QueryBusInterface.php
    │   │   └── Adapter/
    │   │       └── Symfony/
    │   │           ├── SymfonyCommandBus.php
    │   │           └── SymfonyQueryBus.php
    │   └── Subscriber/
    │       └── ExceptionSubscriber.php
    ├── Order/
    │   ├── Http/
    │   │   ├── Action/
    │   │   │   ├── CreateOrderAction.php
    │   │   │   └── GetOrderAction.php
    │   │   ├── Request/
    │   │   │   └── CreateOrderRequest.php
    │   │   └── Transformer/
    │   │       └── OrderTransformer.php
    │   ├── Console/
    │   │   └── CleanupExpiredOrdersCommand.php
    │   ├── Doctrine/
    │   │   ├── Type/
    │   │   │   └── OrderIdType.php
    │   │   └── Repository/
    │   │       └── OrderRepository.php
    │   └── DependencyInjection/
    │       ├── OrderConfiguration.php
    │       └── OrderExtension.php
    ├── Payment/
    │   ├── Http/
    │   │   └── Action/
    │   │       └── ProcessPaymentAction.php
    │   ├── Doctrine/
    │   │   └── Repository/
    │   │       └── PaymentRepository.php
    │   └── Gateway/
    │       ├── PaymentGatewayInterface.php
    │       └── StripePaymentGateway.php
    └── User/
        ├── Http/
        │   └── Action/
        │       └── RegisterUserAction.php
        ├── Doctrine/
        │   └── Repository/
        │       └── UserRepository.php
        └── Security/
            ├── Authenticator/
            │   └── UsernamePasswordAuthenticator.php
            ├── Voter/
            │   └── OrderVoter.php
            └── Provider/
                └── UserProvider.php
```
