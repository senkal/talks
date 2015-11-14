# Symfony Console
- Can be used in isolation
- Easy to integrate because of cron jobs nature
- Small chance to introduce bugs in other part of the application
- Can still use buggy, legacy code

^

## Legacy cron code

```php
    if ('cli' !== PHP_SAPI) {
       die('Not a cli');
    }
    $deleteQty = 0;
    $galeryId = $argv[1];
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
