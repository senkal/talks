## Integration examples

### Use container
```php
function getParameter(\AppKernel $kernel, $id) {
    return $kernel->getContainer()->getParameter($id);
}

function hasService(\AppKernel $kernel, $id) {
    return $kernel->getContainer()->has($id);
}

function getService(\AppKernel $kernel, $id) {
    return $kernel->getContainer()->getService($id);
}

function getLogger() {
    return getService('logger');
}

```

^

### Share twig extensions
```php
//...
$twig = new Twig_Environment($loader);
$twig->addExtension(getService('twig.your_company.extension'));
//...
```
### Sub Requests
```php
function getInlineRender($uri)
{
        $inlineRenderer = getService('fragment.renderer.inline');
        $request = Request::create($uri, 'GET', array(), $_COOKIE, $_FILES, $_SERVER, '');
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
