# DependencyInjection -- Configuration

## Context Configuration

```php
final class PaymentConfiguration implements ConfigurationInterface
{
    public function getConfigTreeBuilder(): TreeBuilder
    {
        $treeBuilder = new TreeBuilder('payment');

        $treeBuilder->getRootNode()
            ->children()
                ->scalarNode('gateway_url')->isRequired()->end()
                ->scalarNode('api_key')->isRequired()->end()
                ->integerNode('timeout')->defaultValue(30)->end()
            ->end();

        return $treeBuilder;
    }
}
```

## Root Configuration Orchestrator

```php
final class Configuration implements SymfonyConfigurationInterface
{
    /** @param ConfigurationInterface[] $configurations */
    public function __construct(private readonly array $configurations) {}

    public function getConfigTreeBuilder(): TreeBuilder
    {
        $treeBuilder = new TreeBuilder('app');
        $rootNode = $treeBuilder->getRootNode();

        foreach ($this->configurations as $configuration) {
            $rootNode->append($configuration->getConfigTreeBuilder()->getRootNode());
        }

        return $treeBuilder;
    }
}
```
