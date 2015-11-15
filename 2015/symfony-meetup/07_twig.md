# Twig - template engine
- Can be used in isolation
- Good way to introduce MVC to developers
- Can be shard between legacy application and full Symfony stack
- Do when can't do all 


Check documentation [Twig](http://twig.sensiolabs.org/)

^

## Example

```php
$loader = new Twig_Loader_Filesystem('/path/to/templates');
$twig = new Twig_Environment($loader, array(
    'cache' => '/path/to/compilation_cache',
));

echo $twig->render('index.html', array('name' => 'Not Fabien'));
```

