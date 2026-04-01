# Application Exception

```
Exception/
├── AccessDeniedException.php
├── InvalidRequestException.php
└── RateLimitExceededException.php
```

```php
final class InvalidRequestException extends \RuntimeException
{
    public static function missingField(string $field): self
    {
        return new self(sprintf('Required field "%s" is missing.', $field));
    }
}
```
