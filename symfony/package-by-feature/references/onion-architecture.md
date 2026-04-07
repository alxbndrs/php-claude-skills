# Package by Feature + Onion Architecture

## Contents
- Shared -- Domain (base models, VOs), Infrastructure (Doctrine types, MessageBus, external clients)
- Feature contexts -- Domain (Entities, Repository interfaces, Enums) + Application (Handlers, Services, Components) + Infrastructure (HTTP, Console, Doctrine repos, DI, Security)

**Dependencies:** Infrastructure -> Application -> Domain -> Nothing

```
src/
├── Shared/
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
│       ├── GPT/
│       │   ├── GPTClientInterface.php
│       │   └── GPTClient.php
│       └── Subscriber/
│           └── ExceptionSubscriber.php
│
├── Order/
│   ├── Domain/
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
│   ├── Application/
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
│   └── Infrastructure/
│       ├── Http/
│       │   ├── Action/
│       │   │   ├── CreateOrderAction.php
│       │   │   └── GetOrderAction.php
│       │   ├── Request/
│       │   │   └── CreateOrderRequest.php
│       │   └── Transformer/
│       │       └── OrderTransformer.php
│       ├── Console/
│       │   └── CleanupExpiredOrdersCommand.php
│       ├── Doctrine/
│       │   ├── Type/
│       │   │   └── OrderIdType.php
│       │   └── Repository/
│       │       └── OrderRepository.php
│       └── DependencyInjection/
│           ├── OrderConfiguration.php
│           └── OrderExtension.php
│
├── Payment/
│   ├── Domain/
│   │   ├── Model/
│   │   │   ├── Payment.php
│   │   │   └── PaymentId.php
│   │   ├── Repository/
│   │   │   └── PaymentRepositoryInterface.php
│   │   └── Enum/
│   │       └── PaymentStatus.php
│   ├── Application/
│   │   └── Handler/
│   │       └── ProcessPayment/
│   │           ├── ProcessPaymentCommand.php
│   │           └── ProcessPaymentCommandHandler.php
│   └── Infrastructure/
│       ├── Http/
│       │   └── Action/
│       │       └── ProcessPaymentAction.php
│       ├── Doctrine/
│       │   └── Repository/
│       │       └── PaymentRepository.php
│       └── Gateway/
│           ├── PaymentGatewayInterface.php
│           └── StripePaymentGateway.php
│
└── User/
    ├── Domain/
    │   ├── Model/
    │   │   ├── User.php
    │   │   └── UserId.php
    │   └── Repository/
    │       └── UserRepositoryInterface.php
    ├── Application/
    │   └── Handler/
    │       └── RegisterUser/
    │           ├── RegisterUserCommand.php
    │           └── RegisterUserCommandHandler.php
    └── Infrastructure/
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
