# DependencyInjection -- CompilerPass

```php
final class PaymentGatewayCompilerPass implements CompilerPassInterface
{
    public function process(ContainerBuilder $container): void
    {
        if (!$container->has(PaymentGatewayRegistry::class)) {
            return;
        }

        $definition = $container->findDefinition(PaymentGatewayRegistry::class);
        $taggedServices = $container->findTaggedServiceIds('app.payment_gateway');

        foreach ($taggedServices as $id => $tags) {
            $definition->addMethodCall('register', [new Reference($id)]);
        }
    }
}
```
