# Entity and Identity Value Object

## Entity

```php
#[ORM\Entity]
#[ORM\Table(name: 'orders')]
class Order
{
    #[ORM\Id]
    #[ORM\Column(type: OrderIdType::NAME)]
    private OrderId $id;

    #[ORM\Column(type: 'string', enumType: OrderStatus::class)]
    private OrderStatus $status;

    #[ORM\Column(type: 'integer')]
    private int $totalAmount;

    #[ORM\Column(type: 'string', length: 3)]
    private string $currency;

    #[ORM\Column(type: 'datetime_immutable')]
    private \DateTimeImmutable $createdAt;

    public function __construct(OrderId $id, Money $total)
    {
        $this->id = $id;
        $this->status = OrderStatus::pending;
        $this->totalAmount = $total->getAmount();
        $this->currency = $total->getCurrency();
        $this->createdAt = new \DateTimeImmutable();
    }

    public function getId(): OrderId { return $this->id; }

    public function getStatus(): OrderStatus { return $this->status; }

    public function confirm(): void
    {
        if ($this->status !== OrderStatus::pending) {
            throw OrderException::cannotConfirm($this->id);
        }
        $this->status = OrderStatus::confirmed;
    }

    public function cancel(): void
    {
        $this->status = OrderStatus::cancelled;
    }
}
```

## Identity Value Object

```php
final readonly class OrderId
{
    private string $value;

    public function __construct(?string $value = null)
    {
        $this->value = $value ?? Uuid::v4()->toRfc4122();
    }

    public function toString(): string
    {
        return $this->value;
    }

    public function equals(self $other): bool
    {
        return $this->value === $other->value;
    }

    public function __toString(): string
    {
        return $this->value;
    }
}
```
