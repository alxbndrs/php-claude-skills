# Request DTO

```php
final readonly class CreateOrderRequest
{
    #[Assert\NotBlank]
    public string $productId;

    #[Assert\Positive]
    public int $quantity;

    #[Assert\NotBlank]
    #[Assert\Currency]
    public string $currency;
}
```
