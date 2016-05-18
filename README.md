## FastBoot Redis Cache

This cache for the [FastBoot App Server][app-server] works with a Redis
cluster to cache the results of rendered FastBoot pages.

[app-server]: https://github.com/ember-fastboot/fastboot-app-server

To use the cache, configure it with a Redis host and/or port:

```js
const FastBootAppServer = require('fastboot-app-server');
const RedisCache = require('fastboot-redis-cache');

let cache = new RedisCache({
  host: FASTBOOT_REDIS_HOST,
  port: FASTBOOT_REDIS_PORT
});

let server = new FastBootAppServer({
  cache: cache
});
```

When an incoming request arrives, the App Server will consult the
cache for the given route. If it doesn't exist, the page will be
rendered and saved in Redis provided it does not have a 4xx or 5xx
HTTP status code. By default, cached pages are set to expire
after 5 minutes. You can change the expiry by setting the `expiration`
option (in seconds):

```js
let cache = new RedisCache({
  host: FASTBOOT_REDIS_HOST,
  port: FASTBOOT_REDIS_PORT,
  expiration: 60 // one minute
});
```

Additionally, if you would like your cache key to vary based on
information in the request (like headers), you can 
provide a `cacheKey(path, request)` function that takes in as
parameters the path being requested and the request object.

```js
let cache = new RedisCache({
  host: FASTBOOT_REDIS_HOST,
  port: FASTBOOT_REDIS_PORT,
  cacheKey(path, request) {
    return `${path}_${request && request.cookies && request.cookies.chocolateChip}`;
  }
});
```
