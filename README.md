# Solution for Getting the Real User's IP in Laravel Octane

When using Laravel Octane, the `$request->getClientIp()` method returns the IP address of your host instead of the real user's IP address.

To forward the user's real IP address in Laravel Octane, you can create a middleware:
```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class OctaneTrustProxies
{
    public function handle(Request $request, Closure $next): Response
    {
        if ($request->header('x-forwarded-for')) {
            $clientIps = explode(',', $request->header('x-forwarded-for'));
            $request->server->set('REMOTE_ADDR', trim(end($clientIps)));
        }

        return $next($request);
    }
}
```

Then, add your middleware to Kernel.php in the middleware array, before the middlewares that require the real user's IP address:
```php

    protected $middleware = [
        \App\Http\Middleware\OctaneTrustProxies::class,
        // middlewares that require the real user's IP address

    ];
```


