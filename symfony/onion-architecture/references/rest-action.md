# REST Action (ADR Pattern)

One class per endpoint. Uses Action-Domain-Responder pattern.

```php
#[Route(path: '/orders/{id}', methods: ['GET'])]
final readonly class GetOrderAction
{
    public function __construct(
        private QueryBusInterface $queryBus,
        private OrderTransformer $transformer,
    ) {}

    public function __invoke(string $id): JsonResponse
    {
        $this->queryBus->dispatch($query = new GetOrderQuery(new OrderId($id)));

        return new JsonResponse($this->transformer->transform($query->getResult()));
    }
}
```