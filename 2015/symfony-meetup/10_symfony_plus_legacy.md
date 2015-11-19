```php
function bootSymfonyKernel($projectDir, $environment, $debug) {
    require_once $projectDir.'/app/bootstrap.php.cache';
    require_once $projectDir.'/app/AppKernel.php';

    $kernel = new \AppKernel($environment, $debug);
    $kernel->boot();
    if ('dev' === $environment) 
    {
        Debug::enable();
    }

    $request = Request::createFromGlobals();
    $request->attributes->set('is_legacy', true);

    $request->server->set('SCRIPT_FILENAME', 'app.php');

    $container = $kernel->getContainer();
    $container->enterScope('request');
    $container->get('request_stack')->push($request);
    $container->set('request', $request);
    $getResponseEvent = dispatchGetResponseEvent($kernel, $request); //kernel.request
    handleRedirectResponse($kernel, $getResponseEvent->getResponse());
    
    return $kernel;
}

Please check Jakub Zalas presentation [To kernel and back](https://speakerdeck.com/jakzal/into-the-kernel-and-back
```

^

## App flow

### Init
```php
    require_once $projectDir.'/app/bootstrap.php.cache';
    require_once $projectDir.'/app/AppKernel.php';

    $kernel = new \AppKernel($environment, $debug);
    $kernel->boot();
```

### Event dispatcher
```php
function dispatchEvent(\AppKernel $kernel, $event, $eventName)
{
    $eventDispatcher = $kernel->getContainer()->get('event_dispatcher');
    $eventDispatcher->dispatch($eventName, $event);
}
```

^

### Track legacy request
```php
    $request = Request::createFromGlobals();
    $request->attributes->set('is_legacy', true);
```

#### WHy?
```php
    // kernel::handle()
    // load controller
    if (false === $controller = $this->resolver->getController($request)) {
        throw new NotFoundHttpException(sprintf('Unable to find the controller for path "%s". The route is wrongly configured.', $request->getPathInfo()));
    }
```

#### How?
```php
    public function onKernelRequest(GetResponseEvent $event)
    {
        $request = $event->getRequest();

        try {
            parent::onKernelRequest($event);
        } catch (NotFoundHttpException $e) {
            if (null === $request->attributes->get('is_legacy')) {
                throw $e;
            }
        }

    }
```

^

### Symfony requirement
```php
    $request->server->set('SCRIPT_FILENAME', 'app.php');
```

####Why?
```php
    //Request::getBaseUrl()::prepareBaseUrl()
    $filename = basename($this->server->get('SCRIPT_FILENAME'));
```

### Request stack - container init
```php
    $container = $kernel->getContainer();
    $container->enterScope('request');
    $container->get('request_stack')->push($request);
    $container->set('request', $request);
```

^

### kernel.request
```php
$getResponseEvent = dispatchGetResponseEvent($kernel, $request); //kernel.request

function dispatchGetResponseEvent(\AppKernel $kernel, $request)
{
    $event = new GetResponseEvent($kernel, $request, HttpKernelInterface::MASTER_REQUEST);
    dispatchEvent($kernel, $event, KernelEvents::REQUEST);

    return $event;
}
```

### Redirect response
dispatch  filter response event(kernel.response)
```php
    $event = new FilterResponseEvent($kernel, $request, HttpKernelInterface::MASTER_REQUEST, $response);
```

dispatch kernel.terminate event
```
    $event = new PostResponseEvent($kernel, $request, $response);
```  
