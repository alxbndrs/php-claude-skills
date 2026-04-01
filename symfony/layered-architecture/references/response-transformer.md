# Response Transformer

Transforms domain models to API response arrays.

```php
final readonly class OrderTransformer
{
    public function __construct(
        private OrderItemTransformer $itemTransformer,
    ) {}

    public function transform(Order $order): array
    {
        return [
            'id' => $order->getId()->toString(),
            'status' => $order->getStatus()->value,
            'total' => $order->getTotal()->getAmount(),
            'items' => array_map(
                fn (OrderItem $item) => $this->itemTransformer->transform($item),
                $order->getItems(),
            ),
            'createdAt' => $order->getCreatedAt()->format('c'),
        ];
    }
}
```
