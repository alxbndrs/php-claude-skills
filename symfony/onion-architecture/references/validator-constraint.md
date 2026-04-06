# Validator -- Constraint and ConstraintValidator

## Constraint

```php
#[Attribute]
final class UniqueEmail extends Constraint
{
    public string $message = 'Email "{{ value }}" is already registered.';
}
```

## ConstraintValidator

```php
final class UniqueEmailValidator extends ConstraintValidator
{
    public function __construct(
        private readonly UserRepositoryInterface $userRepository,
    ) {}

    public function validate(mixed $value, Constraint $constraint): void
    {
        if (!$constraint instanceof UniqueEmail || $value === null) {
            return;
        }

        if ($this->userRepository->existsByEmail($value)) {
            $this->context->buildViolation($constraint->message)
                ->setParameter('{{ value }}', $value)
                ->addViolation();
        }
    }
}
```
