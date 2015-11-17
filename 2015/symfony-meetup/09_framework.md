# When to use full framework
- Doctrine
- Routing
- HttpKernel

^

### Doctrine bootstrap

```php

$isDevMode = true;
$config = Setup::createXMLMetadataConfiguration(
    array(__DIR__."/src/MyApp/DoctrineBridge/Mapping"),
    $isDevMode
);

// the connection configuration
$dbParams = array(
    'driver'   => 'pdo_mysql',
    'user'     => $cfg['db']['user'],
    'password' => $cfg['db']['password'],
    'dbname'   => $cfg['db']['database'],
);
$entityManager = EntityManager::create($dbParams, $config);

//...

$customEntityRepository = $entityManager->getRepository(CustomEntity::class);
```

^

### Routing bootstrap

```php

$routes = new RouteCollection();

$route = new Route(
    '/',
    array('_controller' => 'MyApp\\Controller\\HomeController::indexAction')
);
$routes->add('homepage', $route);

$context = new RequestContext('/');
$matcher = new UrlMatcher($routes, $context);
$generator = new UrlGenerator($routes, $context);

$parameters = $matcher->match($_SESSION['REQUEST_URI']);
//...


echo call_user_func([$controller, $method]);
```

^

### HttpKernel

<img src="public/kernel-flow.png" alt="Kernel flow" />
