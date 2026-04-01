# CQRS -- Query and QueryHandler

## Query

```php
class GetOrderQuery
{
    private ?Order $result = null;

    public function __construct(
        public readonly OrderId $orderId,
    ) {}

    public function getResult(): Order { return $this->result; }
    public function setResult(Order $result): void { $this->result = $result; }
}
```

## QueryHandler

```php
#[AsMessageHandler]
final readonly class GetOrderQueryHandler
{
    public function __construct(
        private OrderRepositoryInterface $orderRepository,
    ) {}

    public function __invoke(GetOrderQuery $query): void
    {
        $order = $this->orderRepository->findById($query->orderId);

        $query->setResult($order);
    }
}
```
