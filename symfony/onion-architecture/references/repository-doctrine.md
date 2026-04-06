# Repository -- Doctrine Implementation

Lives in Infrastructure layer. Implements the interface defined in Domain.

```php
final readonly class OrderRepository implements OrderRepositoryInterface
{
    public function __construct(
        private EntityManagerInterface $em,
    ) {}

    public function findById(OrderId $id): Order
    {
        $order = $this->em->find(Order::class, $id);

        if ($order === null) {
            throw OrderException::notFound($id);
        }

        return $order;
    }

    public function save(Order $order): void
    {
        $this->em->persist($order);
        $this->em->flush();
    }

    public function delete(Order $order): void
    {
        $this->em->remove($order);
        $this->em->flush();
    }

    public function findByStatus(OrderStatus $status): array
    {
        return $this->em->createQueryBuilder()
            ->select('o')
            ->from(Order::class, 'o')
            ->where('o.status = :status')
            ->setParameter('status', $status)
            ->getQuery()
            ->getResult();
    }
}
```

## InMemory Implementation (Testing)

```php
final class InMemoryOrderRepository implements OrderRepositoryInterface
{
    /** @var Order[] */
    private array $orders = [];

    public function findById(OrderId $id): Order
    {
        foreach ($this->orders as $order) {
            if ($order->getId()->equals($id)) {
                return $order;
            }
        }

        throw OrderException::notFound($id);
    }

    public function save(Order $order): void
    {
        $this->orders[$order->getId()->toString()] = $order;
    }

    public function delete(Order $order): void
    {
        unset($this->orders[$order->getId()->toString()]);
    }

    public function findByStatus(OrderStatus $status): array
    {
        return array_filter(
            $this->orders,
            fn (Order $o) => $o->getStatus() === $status,
        );
    }
}
```
