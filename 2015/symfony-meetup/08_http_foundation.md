# Http Foundation
- Can be used in isolation
- Object oriented, standardized approach to global variables
- Abstract Session handling with nice interface
- Cover edge cases 
- Heart of what PHP was created for
- Tests - can mock/fake request

^

## Request/Response

Request
```php
    request: ParameterBag;
    query: ParameterBag;
    cookies: ParameterBag;
    attributes: ParameterBag;
    files: FileBag;
    server: ServerBag;
    headers: HeaderBag.
```
   
Response
```php
$response->prepare($request); //is valid HTTP response
$response->send() // send headers and display content
```

^

## Example

```php
use Symfony\Component\HttpFoundation\Request;

require __DIR__ . '/vendor/autoload.php';

$_GET['var'] = '123';
$request = Request::createFromGlobals();

echo $request->query->get('var', 'NONE');
// 123

$_GET['var'] = 'abcde';

echo $request->query->get('var', 'NONE');
// 123

echo $_GET['var'];
// abcde

```

^

## Session API

- Abstract php session related function
- Do not use legacy session at the same time
- Ability to mock session- `MockFileSessionStorage`
- Session storage handler:
    - PdoSessionHandler
    - MemcacheSessionHandler
    - MemcachedSessionHandler
    - MongoDbSessionHandler
    - NullSessionHandler

^

## Proxy handling

#### Load balancing/Varnish/3rd party proxies?

```php
// only trust proxy headers coming from this IP addresses
Request::setTrustedProxies(array('192.0.0.1', '10.0.0.0/8'));
```

```php
    X-Forwarded-For Used in getClientIp();
    X-Forwarded-Host Used in getHost();
    X-Forwarded-Port Used in getPort();
    X-Forwarded-Proto Used in getScheme() and isSecure();
```

```php
//isSecure()
        if ($this->isFromTrustedProxy() && self::$trustedHeaders[self::HEADER_CLIENT_PROTO] && $proto = $this->headers->get(self::$trustedHeaders[self::HEADER_CLIENT_PROTO])) {
            return in_array(strtolower(current(explode(',', $proto))), array('https', 'on', 'ssl', '1'));
        }

        $https = $this->server->get('HTTPS');

        return !empty($https) && 'off' !== strtolower($https);
```

^

#### Get client IP

```php

    public function getClientIps()
    {
        $clientIps = array();
        $ip = $this->server->get('REMOTE_ADDR');

        if (!$this->isFromTrustedProxy()) {
            return array($ip);
        }

        if (self::$trustedHeaders[self::HEADER_FORWARDED] && $this->headers->has(self::$trustedHeaders[self::HEADER_FORWARDED])) {
            $forwardedHeader = $this->headers->get(self::$trustedHeaders[self::HEADER_FORWARDED]);
            preg_match_all('{(for)=("?\[?)([a-z0-9\.:_\-/]*)}', $forwardedHeader, $matches);
            $clientIps = $matches[3];
        } elseif (self::$trustedHeaders[self::HEADER_CLIENT_IP] && $this->headers->has(self::$trustedHeaders[self::HEADER_CLIENT_IP])) {
            $clientIps = array_map('trim', explode(',', $this->headers->get(self::$trustedHeaders[self::HEADER_CLIENT_IP])));
        }

    //...
       }

        // Now the IP chain contains only untrusted proxies and the client IP
        return $clientIps ? array_reverse($clientIps) : array($ip);
```
