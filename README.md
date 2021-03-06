Elefrant-redis-cache
-------------
Based on the query string and [restify-cache](https://github.com/gergelyke/restify-cache), it will store/retrieve JSON responses from Redis.


### Usage ###

```
var cache = require('elefrant-redis-cache');
cache.config({
redisPort: 6379,        //default: '6379'
redisHost: 'localhost', //default: 'localhost'
redisOptions: {},       //optional
ttl: 60 * 60            //default:  60 * 60; in seconds
});
```

The first middleware after auth (if there is any) should be the cache's before.

```
server.use(cache.before);
```

You have to subscribe for the server's after event as well.

__WARNING! In your route handlers, you always have to call `next()`!__

```
server.on('after', cache.after);
```

### Cache Control ###
Use of Restify's [res.cache()](http://mcavage.me/node-restify/#Response-API) method will control the [EXPIRE](http://redis.io/commands/expire) time in Redis.  The absence of a response cache will use the **cache.config.ttl** value identified above.

Indicates that the response should be cached for 600 seconds. (PUBLIC | PRIVATE | NO-CACHE | NO-STORE)
```
res.cache('public', {maxAge: 600});
```

A maxAge value of 0 will engage Redis, but set the expire seconds to 0 (essentially expiring immediately).
```
res.cache('public', 0);
```

### Additional Headers ###
A header is added to each response:

* __X-Cache: HIT__ - the response was served from cache
* __X-Cache: MISS__ - the response generation fell through to the endpoint
