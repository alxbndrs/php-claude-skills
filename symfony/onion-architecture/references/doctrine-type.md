# Doctrine -- Custom Type

```php
final class OrderIdType extends StringType
{
    public const NAME = 'order_id';

    public function getName(): string
    {
        return self::NAME;
    }

    public function convertToPHPValue($value, AbstractPlatform $platform): ?OrderId
    {
        return $value !== null ? new OrderId($value) : null;
    }

    public function convertToDatabaseValue($value, AbstractPlatform $platform): ?string
    {
        return $value instanceof OrderId ? $value->toString() : $value;
    }
}
```
