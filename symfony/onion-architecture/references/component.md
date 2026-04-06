# Component

Private internal services for rich business logic. Not used directly by other modules. Structure is arbitrary per component needs.

```
Component/
└── FeeCalculator/
    ├── FeeCalculatorInterface.php
    ├── PercentageFeeCalculator.php
    ├── FlatFeeCalculator.php
    └── CompositeFeeCalculator.php
```

```php
interface FeeCalculatorInterface
{
    public function calculate(Money $amount): Money;
}
```

```php
final readonly class PercentageFeeCalculator implements FeeCalculatorInterface
{
    public function __construct(
        private float $rate,
    ) {}

    public function calculate(Money $amount): Money
    {
        return $amount->multiply($this->rate);
    }
}
```

```php
final readonly class FlatFeeCalculator implements FeeCalculatorInterface
{
    public function __construct(
        private Money $fee,
    ) {}

    public function calculate(Money $amount): Money
    {
        return $this->fee;
    }
}
```

```php
final readonly class CompositeFeeCalculator implements FeeCalculatorInterface
{
    /** @param FeeCalculatorInterface[] $calculators */
    public function __construct(
        private array $calculators,
    ) {}

    public function calculate(Money $amount): Money
    {
        $total = new Money(0, $amount->getCurrency());

        foreach ($this->calculators as $calculator) {
            $total = $total->add($calculator->calculate($amount));
        }

        return $total;
    }
}
```
