# GPT Client

Generic client provides message sending and JSON response decoding. Knows nothing about specific prompts or domain DTOs.

```php
interface GPTClientInterface
{
    public function send(GPTRequest $request): GPTResponse;
}
```

```php
final readonly class GPTResponse
{
    public function __construct(
        private string $rawContent,
    ) {}

    public function toArray(): array
    {
        return json_decode($this->rawContent, true, 512, JSON_THROW_ON_ERROR);
    }

    public function toString(): string
    {
        return $this->rawContent;
    }
}
```

Domain-specific usage (prompts, DTO mapping, business rules) lives in the owning domain context:

```
Domain/
└── Order/
    └── Component/
        └── PriceEstimator/
            ├── PriceEstimatorInterface.php
            └── GPTPriceEstimator.php     # Uses GPTClientInterface, builds prompts,
                                          # parses GPTResponse into domain DTOs
```
