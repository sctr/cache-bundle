Aequasi cache-bundle
====================

#### Cache Bundle for Symfony 2

Creates services in Symfony 2, for cache, that can also be used with doctrines three cache types (metadata, result, and query). It also provides functionality for session handler support, and Router support.

Should work in all versions of Symfony, and php 5.3

The respective cache extensions will be required for your project.

Redis uses the php redis extension.

#### Requirements

- PHP 5.3.x or 5.4.x

#### To Install

```sh
	composer.phar require aequasi/cache-bundle dev-master
```

Add the bundle to app/AppKernel.php

```php
$bundles(
    ...
       new Aequasi\Bundle\CacheBundle\CacheBundle(),
    ...
);
```

Then add parameters (probably in config.yml) for your servers, and options

```yml
aequasi_cache:
    instances:
        default:
          persistent: true
          namespace: mc
          type: memcached
          hosts:
              - { host: localhost, port: 11211 }
```

To see all the config options, run `php app/console config:dump-reference aequasi_cache` to view the config settings


#### Doctrine

This bundle allows you to use its services for Doctrine's caching methods of metadata, result, and query.

If you want doctrine to use this as the result and query cache, add this

```yml
aequasi_cache:
    doctrine:
        metadata:
            instance: default
            entity_manager: default          # the name of your entity_manager connection
            document_manager: default        # the name of your document_manager connection
        result:
            instance: default
            entity_manager: [default, read]  # you may specify multiple entity_managers
            prefix: "result_"                # you may specify a prefix for the entries
        query:
            instance: default
            entity_manager: default
```

#### Session

This bundle even allows you to store your session data in one of your cache clusters. To enable:

```yml
aequasi_cache:
    session:
        instance: default
        prefix: "session_"
        ttl: 7200
```

#### Router

This bundle also provides router caching, to help speed that section up. To enable:

```yml
aequasi_cache:
    router:
        enable: true
        instance: default
```

If you change any of your routes, you will need to clear all of the route_* keys in your cache.


#### To Use

You can use the default cache functions, doctrine's `useResultCache` and `useQueryCache`, along with the default doctrine functions. Heres an example

```php
$cache = $this->get( 'aequasi_cache.instance.default' );
$key = 'test';
if( $data = $cache->fetch( $key ) ) {
	print_r( $data );
} else {
	/** @var $em \Doctrine\ORM\EntityManager */
	$data = $em->find( 'AcmeDemoBundle:User', 1 );
	$cache->save( $key, $data, 3600 );
}
```

There is also the `cache()` function on the service that allows you to wrap the above, into a single function

```php
$cache = $this->get( 'aequasi_cache.instance.default' );
$user = $cache->cache( 'test', function() use( $em ) { return $em->find( "AcmeDemoBundle:User", 1 ); }, 3600 );
var_dump( $user );
```

### Need Help?

Create an issue if you've found a bug,

or email me at aequasi@gmail.com
