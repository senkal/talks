# Symfony Console
- Can be used in isolation
- Easy to integrate because of cron jobs nature
- Small chance to introduce bugs in other part of the application
- Can still use legacy code

^

## Legacy cron code

```php
    if ('cli' !== PHP_SAPI) {
       die('Not a cli');
    }
    $deleteQty = 0;
    $galleryId = $argv[1];
    $checkQ = mysql_query("SELECT * FROM galery WHERE galery_id='$galeryId' LIMIT 1") 
        or die(mysql_error());
    
    while ($checkR = mysql_fetch_assoc($checkQ) ) {
        ...
        if (0 == $catCheckR[0]) {
            $updateQ = mysql_query("UPDATE categories SET galery=0 WHERE id='$catId' ")
                or die(mysql_error());
        }
        ++$deleteQty;
    }
    
    $message = sprintf('Deleted %d items', $deletedQty);
    $mail = mail('cron@example.com', 'Gallery delete statistics', $message);
```

^

##Symfony console setup

```yaml
  "autoload" : {
    "psr-4": { "": "src" }
  },
```

```bash
composer require symfony/console
```

```bash
symfony/console suggests installing symfony/event-dispatcher ()
symfony/console suggests installing symfony/process ()
symfony/console suggests installing psr/log (For using the console logger)
```

```php
#!/usr/bin/env php
<?php
    namespace MyApp\Command;
    //app/console or bin/console
    require __DIR__ . '/../vendor/autoload.php';
    
    use Symfony\Component\Console\Application;

    $application = new Application();
    $application->add(new DeleteGalleryCommand());
    $application->run();
```

^

##Command example

```php

use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Input\InputArgument;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;

class DeleteGalleryCommand extends Command
{
    protected function configure()
    {
        $this->setName('namespace:gallery:delete')
            ->addArgument(
                'gallery',
                InputArgument::REQUIRED
            );
    }
    
    protected function execute(InputInterface $input, OutputInterface $outputInterface)
    {
        $galleryId = $input->getArgument('galleryId');
        //...
        // all legacy PHP code
    }
```

^

##Commands plus events

```bash
composer require symfony/event-dispatcher
```

```bash
symfony/event-dispatcher suggests installing symfony/dependency-injection ()
symfony/event-dispatcher suggests installing symfony/http-kernel ()
```

```php
use Symfony\Component\EventDispatcher\EventDispatcher;
use Symfony\Component\Console\ConsoleEvents;

$dispather = new EventDispatcher();
$dispather->addListener(ConsoleEvents::COMMAND, [$consoleListener, 'onCommandStart']);
$dispather->addListener(ConsoleEvents::TERMINATE, [$consoleListener, 'onCommandTerminate']);
$dispather->addListener(ConsoleEvents::EXCEPTION, [$consoleListener, 'onCommandException']);

$application = new Application();
$application->setDispatcher($dispatcher);

...
$application->run();
```

^

## Listener implementation example

```php
namespace MyApp\Listener;

use Symfony\Component\Console\Event\ConsoleCommandEvent;
use Symfony\Component\Console\Event\ConsoleExceptionEvent;
use Symfony\Component\Console\Event\ConsoleTerminateEvent;

class ConsoleListener
{
    public function onCommandStart(ConsoleCommandEvent $event)
    {
    }
    public function onCommandTerminate(ConsoleTerminateEvent $event)
    {
    }
    public function onCommandException(ConsoleExceptionEvent $event)
    {
    }

}
```


