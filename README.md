# adminer on PipeOps

# What is Adminer?
Adminer (formerly phpMinAdmin) is a full-featured database management tool written in PHP. Conversely to phpMyAdmin, it consist of a single file ready to deploy to the target server. Adminer is available for MySQL, PostgreSQL, SQLite, MS SQL, Oracle, Firebird, SimpleDB, Elasticsearch and MongoDB.

[![Deploy on PipeOps](https://pub-a1fbf367a4cd458487cfa3f29154ac93.r2.dev/Default.png)](https://railway.app/template/0ELOuE?referralCode=IQhE0B)



![](https://raw.githubusercontent.com/docker-library/docs/95569c9119afe7b11a233105d398f99d93d2fcce/adminer/logo.png)


# How to use this image


## Standalone

     $ docker run --link some_database:db -p 8080:8080 adminer

Then you can hit http://localhost:8080 or http://host-ip:8080 in your browser.


## FastCGI

If you are already running a FastCGI capable web server you might prefer running Adminer via FastCGI:

     $ docker run --link some_database:db -p 9000:9000 adminer:fastcgi

Then point your web server to port 9000 of the container.

Note: This exposes the FastCGI socket to the Internet. Make sure to add proper firewall rules or use a private Docker network instead to prevent a direct access.


... via docker-compose https://github.com/docker/compose or docker stack deploy https://docs.docker.com/engine/reference/commandline/stack_deploy/


Example docker-compose.yml for adminer:

     # Use root/example as user/password credentials

     version: '3.1'

     services:

          adminer:
          image: adminer
          restart: always
          ports:
               - 8080:8080

     db:
          image: mysql:5.6
          restart: always
          environment:
          MYSQL_ROOT_PASSWORD: example




Run docker stack deploy -c stack.yml adminer (or docker-compose -f stack.yml up), wait for it to initialize completely, and visit http://swarm-ip:8080, http://localhost:8080, or http://host-ip:8080 (as appropriate).



## Loading plugins

This image bundles all official Adminer plugins. You can find the list of plugins on GitHub: https://github.com/vrana/adminer/tree/master/plugins.

To load plugins you can pass a list of filenames in ADMINER_PLUGINS:

     $ docker run --link some_database:db -p 8080:8080 -e ADMINER_PLUGINS='tables-filter tinymce' adminer


If a plugin requires parameters to work correctly instead of adding the plugin to ADMINER_PLUGINS, you need to add a custom file to the container:


```
     $ docker run --link some_database:db -p 8080:8080 -e ADMINER_PLUGINS='login-servers' adminer
Unable to load plugin file "login-servers", because it has required parameters: servers
Create a file "/var/www/html/plugins-enabled/login-servers.php" with the following contents to load the plugin:

<?php
require_once('plugins/login-servers.php');

/** Set supported servers
    * @param array array($domain) or array($domain => $description) or array($category => array())
    * @param string
    */
return new AdminerLoginServers(
    $servers = ???,
    $driver = 'server'
);
```


To load a custom plugin you can add PHP scripts that return the instance of the plugin object to /var/www/html/plugins-enabled/.


# Choosing a design

The image bundles all the designs that are available in the source package of adminer. You can find the list of designs on GitHub: https://github.com/vrana/adminer/tree/master/designs.

To use a bundled design you can pass its name in ADMINER_DESIGN:

     $ docker run --link some_database:db -p 8080:8080 -e ADMINER_DESIGN='nette' adminer

To use a custom design you can add a file called /var/www/html/adminer.css.
Usage with external server

You can specify the default host with the ADMINER_DEFAULT_SERVER environment variable. This is useful if you are connecting to an external server or a docker container named something other than the default db.

     docker run -p 8080:8080 -e ADMINER_DEFAULT_SERVER=mysql adminer

# Supported Drivers

While Adminer supports a wide range of database drivers this image only supports the following out of the box:

    MySQL
    PostgreSQL
    SQLite
    SimpleDB
    Elasticsearch

To add support for the other drivers you will need to install the following PHP extensions on top of this image:

    pdo_dblib (MS SQL)
    oci8 (Oracle)
    interbase (Firebird)
    mongodb (MongoDB)
