# Domain Exception

Uses static factory methods.

```php
final class OrderException extends \DomainException
{
    public static function notFound(OrderId $id): self
    {
        return new self(sprintf('Order "%s" not found.', $id));
    }

    public static function cannotConfirm(OrderId $id): self
    {
        return new self(sprintf('Order "%s" cannot be confirmed in its current state.', $id));
    }
}
```
