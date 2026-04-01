# Event Subscriber

```php
final class ExceptionSubscriber implements EventSubscriberInterface
{
    public static function getSubscribedEvents(): array
    {
        return [
            KernelEvents::EXCEPTION => 'onKernelException',
        ];
    }

    public function onKernelException(ExceptionEvent $event): void
    {
        $exception = $event->getThrowable();

        if ($exception instanceof DomainException) {
            $event->setResponse(new JsonResponse(
                ['error' => $exception->getMessage()],
                Response::HTTP_BAD_REQUEST,
            ));
        }
    }
}
```
