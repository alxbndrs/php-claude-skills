# CQRS -- Command and CommandHandler

## Command

```php
class CreateOrderCommand
{
    private ?Order $result = null;

    public function __construct(
        public readonly string $productId,
        public readonly int $quantity,
        public readonly string $currency,
        public readonly string $userId,
    ) {}

    public function getResult(): Order { return $this->result; }
    public function setResult(Order $result): void { $this->result = $result; }
}
```

## CommandHandler

```php
#[AsMessageHandler]
final readonly class CreateOrderCommandHandler
{
    public function __construct(
        private OrderRepositoryInterface $orderRepository,
        private PricingServiceInterface $pricingService,
    ) {}

    public function __invoke(CreateOrderCommand $command): void
    {
        $total = $this->pricingService->calculate($command->productId, $command->quantity, $command->currency);

        $order = new Order(new OrderId(), $total);

        $this->orderRepository->save($order);

        $command->setResult($order);
    }
}
```

## Handler Directory Structure

```
Handler/
  CreateOrder/
    CreateOrderCommand.php
    CreateOrderCommandHandler.php
  ConfirmOrder/
    ConfirmOrderCommand.php
    ConfirmOrderCommandHandler.php
  GetOrder/
    GetOrderQuery.php
    GetOrderQueryHandler.php
    Result/
      OrderResult.php
```
