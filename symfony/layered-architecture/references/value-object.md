# Value Object

```php
final readonly class Money
{
    public function __construct(
        private int $amount,
        private string $currency,
    ) {}

    public function getAmount(): int { return $this->amount; }
    public function getCurrency(): string { return $this->currency; }

    public function add(self $other): self
    {
        $this->assertSameCurrency($other);
        return new self($this->amount + $other->amount, $this->currency);
    }

    public function subtract(self $other): self
    {
        $this->assertSameCurrency($other);
        return new self($this->amount - $other->amount, $this->currency);
    }

    public function multiply(float $factor): self
    {
        return new self((int) round($this->amount * $factor), $this->currency);
    }

    private function assertSameCurrency(self $other): void
    {
        if ($this->currency !== $other->currency) {
            throw new \InvalidArgumentException('Currency mismatch.');
        }
    }
}
```
