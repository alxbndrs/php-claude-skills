# MessageBus -- Interfaces and Adapters

```
Infrastructure/
└── MessageBus/
    ├── CommandBusInterface.php
    ├── QueryBusInterface.php
    ├── EventBusInterface.php
    ├── Command/
    │   └── CommandInterface.php
    ├── Query/
    │   └── QueryInterface.php
    ├── Event/
    │   └── EventInterface.php
    └── Adapter/
        └── Symfony/
            └── Messenger/
                ├── SymfonyCommandBus.php
                ├── SymfonyQueryBus.php
                └── SymfonyEventBus.php
```

```php
interface CommandBusInterface
{
    public function dispatch(object $command): void;
}
```

```php
final readonly class SymfonyCommandBus implements CommandBusInterface
{
    public function __construct(
        private MessageBusInterface $messageBus,
    ) {}

    public function dispatch(object $command): void
    {
        $this->messageBus->dispatch($command);
    }
}
```
