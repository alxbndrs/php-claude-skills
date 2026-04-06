# GraphQL Types

## Query Type

```php
#[QueryType]
final readonly class OrderQueryType
{
    public function __construct(
        private OrderRepositoryInterface $orderRepository,
    ) {}

    #[Field]
    public function order(#[Argument] string $id): Order
    {
        return $this->orderRepository->findById(new OrderId($id));
    }
}
```

## Mutation Input

```php
#[Input]
final readonly class CreateOrderInput
{
    #[Field]
    public string $productId;

    #[Field]
    public int $quantity;
}
```

```php
#[MutationType]
final readonly class OrderMutationType
{
    public function __construct(
        private CommandBusInterface $commandBus,
    ) {}

    #[Mutation]
    public function createOrder(CreateOrderInput $input): Order
    {
        $this->commandBus->dispatch($command = new CreateOrderCommand(
            productId: $input->productId,
            quantity: $input->quantity,
        ));

        return $command->getResult();
    }
}
```
