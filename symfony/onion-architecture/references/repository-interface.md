# Repository Interface

Defined in the Domain layer. Implementations live in Infrastructure.

```php
interface OrderRepositoryInterface
{
    public function findById(OrderId $id): Order;
    public function save(Order $order): void;
    public function delete(Order $order): void;

    /** @return Order[] */
    public function findByStatus(OrderStatus $status): array;
}
```
