# OpenEuropa Corporate countries

This module provides integration of EC Corporate countries with Drupal.

The main module offers a corporate countries repository service which returns the Corporate countries data.

**Table of contents:**

- [Sub-modules](#sub-modules)
- [Requirements](#requirements)
- [Installation](#installation)
- [Development setup](#development-setup)
- [Contributing](#contributing)
- [Versioning](#versioning)

## Sub-modules

- [OpenEuropa Corporate countries address](modules/oe_corporate_countries_address/README.md): this module provides integration of the EC corporate countries with the [address](https://www.drupal.org/project/address) module.

## Requirements

This depends on the following software:

* [PHP >=7.2](http://php.net/)
* Virtuoso (or equivalent) triple store which contains the RDF representations of the [Country](https://op.europa.eu/en/web/eu-vocabularies/at-dataset/-/resource/dataset/country) Publications Office (OP) vocabulary

## Installation

This module indirectly depends on the [drupal/sparql_entity_storage](https://www.drupal.org/project/sparql_entity_storage) module, which requires a more updated version of `easyrdf/easyrdf` package.\
First the correct version of this package should be installed:

```bash
composer require "easyrdf/easyrdf 0.10.0-alpha.1 as 0.9.2"
```

Then install this package and its dependencies:

```bash
composer require openeuropa/oe_corporate_countries
```

It is strongly recommended to use the provisioned Docker image for Virtuoso that contains already the OP vocabularies. To do this, add the image to your `docker.compose.yml` file:

```
  sparql:
    image: openeuropa/triple-store-dev
    environment:
    - SPARQL_UPDATE=true
    - DBA_PASSWORD=dba
    ports:
      - "8890:8890"
```

Otherwise, make sure you have the triple store instance running and have imported the required vocabularies.

Next, if you are using the Task Runner to set up your site, add the `runner.yml` configuration for connecting to the triple store. Under the `drupal` key:

```
  sparql:
    host: "sparql"
    port: "8890"
```

Still in the `runner.yml`, add the instruction to create the Drupal settings for connecting to the triple store. Under the `drupal.settings.databases` key:

```
  sparql_default:
    default:
      prefix: ""
      host: ${drupal.sparql.host}
      port: ${drupal.sparql.port}
      namespace: 'Drupal\Driver\Database\sparql'
      driver: 'sparql'
```

Then you can proceed with the regular Task Runner commands for setting up the site.

Otherwise, ensure that in your site's `setting.php` file you have the connection information to your own triple store instance:

```
$databases["sparql_default"] = array(
  'default' => array(
    'prefix' => '',
    'host' => 'your-triple-store-host',
    'port' => '8890',
    'namespace' => 'Drupal\\Driver\\Database\\sparql',
    'driver' => 'sparql'
  )
);
```

## Development setup

You can build a development site using [Docker](https://www.docker.com/get-docker) and
[Docker Compose](https://docs.docker.com/compose/) with the provided configuration.

Docker provides the necessary services and tools such as a web server and a database server to get the site running,\
regardless of your local host configuration.

#### Requirements:

- [Docker](https://www.docker.com/get-docker)
- [Docker Compose](https://docs.docker.com/compose/)

#### Configuration

By default, Docker Compose reads two files, a `docker-compose.yml` and an optional `docker-compose.override.yml` file.
By convention, the `docker-compose.yml` contains your base configuration and it's provided by default.
The override file, as its name implies, can contain configuration overrides for existing services or entirely new
services.
If a service is defined in both files, Docker Compose merges the configurations.

Find more information on Docker Compose extension mechanism on [the official Docker Compose documentation](https://docs.docker.com/compose/extends/).

#### Usage

To start, run:

```bash
docker-compose up
```

It's advised to not daemonize `docker-compose` so you can turn it off (`CTRL+C`) quickly when you're done working.
However, if you'd like to daemonize it, you have to add the flag `-d`:

```bash
docker-compose up -d
```

Then:

```bash
docker-compose exec web composer install
docker-compose exec web ./vendor/bin/run drupal:site-install
```

Using default configuration, the development site files should be available in the `build` directory and the development site
should be available at: [http://127.0.0.1:8080/build](http://127.0.0.1:8080/build).

#### Running the tests

To run the grumphp checks:

```bash
docker-compose exec web ./vendor/bin/grumphp run
```

To run the phpunit tests:

```bash
docker-compose exec web ./vendor/bin/phpunit
```

To run the behat tests:

```bash
docker-compose exec web ./vendor/bin/behat
```

## Contributing

Please read [the full documentation](https://github.com/openeuropa/openeuropa) for details on our code of conduct, and the process for submitting pull requests to us.

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the available versions, see the [tags on this repository](https://github.com/openeuropa/oe_corporate_countries/tags).
