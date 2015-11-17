## Integration examples

### Use container
```php
function getParameter($id) {
    global $kernel;

    return $kernel->getContainer()->getParameter($id);
}

function hasService($id) {
    global $kernel;
    return $kernel->getContainer()->has($id);
}

function getService($id, $kernal) {
    return $kernel->getContainer()->getService($id);
}

function getLogger() {
    return getService('logger');
}

```

^

###Custom events

```php
function dispatchNewOrderEvent($kernel, $customerId, Order $order) {
    $event = new OrderEvent($customerId, $order);
    dispatch_event($kernel, $event, 'order.new');

    return $event;
}
```

### Subrequests
```php
function getInlineRender($uri)
{
        $inlineRenderer = get_service('fragment.renderer.inline');
        $request = \Symfony\Component\HttpFoundation\Request::create($uri, 'GET', array(), $_COOKIE, $_FILES, $_SERVER, '');
        $request->setSession(get_service('request_stack')->getMasterRequest()->getSession());

        return $inlineRenderer->render($uri, $request)->getContent();
}

// view.php
<?= getInlineRender('footer') ?>
```

```twig
//twig file
{{ render(url('footer', {})) }}

```

###Opportunity - ESI
