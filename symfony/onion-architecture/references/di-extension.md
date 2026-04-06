# DependencyInjection -- Extension

## Context Extension

```php
final class PaymentExtension implements ExtensionInterface
{
    public function load(array $config, ContainerBuilder $container): void
    {
        $container->setParameter('payment.gateway_url', $config['payment']['gateway_url']);
        $container->setParameter('payment.api_key', $config['payment']['api_key']);
        $container->setParameter('payment.timeout', $config['payment']['timeout']);
    }
}
```

## Root Extension Orchestrator

```php
final class AppExtension extends Extension
{
    /** @param ExtensionInterface[] $extensions */
    public function __construct(private readonly array $extensions) {}

    public function load(array $configs, ContainerBuilder $container): void
    {
        $configuration = new Configuration(/* ... */);
        $config = $this->processConfiguration($configuration, $configs);

        foreach ($this->extensions as $extension) {
            $extension->load($config, $container);
        }
    }
}
```
