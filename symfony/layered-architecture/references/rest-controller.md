# REST Controller (Classic Pattern)

Multiple endpoints per class.

```php
#[Route(path: '/orders')]
final readonly class OrderController
{
    public function __construct(
        private CommandBusInterface $commandBus,
        private QueryBusInterface $queryBus,
        private OrderTransformer $transformer,
    ) {}

    #[Route(path: '', methods: ['GET'])]
    public function list(): JsonResponse { /* ... */ }

    #[Route(path: '/{id}', methods: ['GET'])]
    public function get(string $id): JsonResponse { /* ... */ }

    #[Route(path: '', methods: ['POST'])]
    public function create(CreateOrderRequest $request): JsonResponse { /* ... */ }
}
```