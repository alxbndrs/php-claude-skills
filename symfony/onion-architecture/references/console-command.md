# Console Command

```php
#[AsCommand(name: 'app:orders:cleanup', description: 'Remove expired pending orders')]
final class CleanupExpiredOrdersCommand extends Command
{
    public function __construct(
        private readonly CommandBusInterface $commandBus,
    ) {
        parent::__construct();
    }

    protected function configure(): void
    {
        $this->addArgument('days', InputArgument::OPTIONAL, 'Days threshold', '30');
    }

    protected function execute(InputInterface $input, OutputInterface $output): int
    {
        $days = (int) $input->getArgument('days');

        $this->commandBus->dispatch(new CleanupOrdersCommand($days));

        $output->writeln(sprintf('Cleaned up orders older than %d days.', $days));

        return Command::SUCCESS;
    }
}
```
