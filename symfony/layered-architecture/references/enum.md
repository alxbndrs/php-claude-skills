# Domain Enum

Cases use `camelCase`.

```php
enum OrderStatus: string
{
    case pending = 'pending';
    case confirmed = 'confirmed';
    case shipped = 'shipped';
    case delivered = 'delivered';
    case cancelled = 'cancelled';

    public function canTransitionTo(self $next): bool
    {
        return match ($this) {
            self::pending => in_array($next, [self::confirmed, self::cancelled]),
            self::confirmed => in_array($next, [self::shipped, self::cancelled]),
            self::shipped => $next === self::delivered,
            default => false,
        };
    }
}
```
