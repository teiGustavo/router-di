## Instalação
[![Source Code](http://img.shields.io/badge/source-coffeecode/router-blue.svg?style=flat-square)](https://github.com/robsonvleite/router)
[![PHP from Packagist](https://img.shields.io/packagist/php-v/coffeecode/router.svg?style=flat-square)](https://packagist.org/packages/coffeecode/router)

Disponível via Composer:
```bash
composer require gteixeira/router
```

## Observações
Foi adicionado o suporte ao PHP-DI, não havendo mudanças na forma como o Router funciona (todas as configurações e formas de se usar continuam de acordo com a documentação, sendo necessária a mudança apenas nos arquivo de Rotas e nos Controllers caso queira utilizar o PHP-DI para resolver as dependências dos construtores).

> Vislumbre de melhoria: Abstrair a lógica de implementação da feat para uma interface, de forma a esperar um método (ex: `get($id)` cujo $id representa o nome da classe com seu namespace ex: `CoffeeCode\Router\Router`) que retorne a instância do objeto $id (assim como o PHP-DI funciona), permitindo ao usuário escolher alguma outra implementação de container de injeção de dependências (desde que siga o contrato estabelecido pela interface).

#### Para mais detalhes, checar a documentação original!!

## Documentação

### Antes:

#### Routes
```php
<?php

use CoffeeCode\Router\Router;

$router = new Router("https://www.youdomain.com");

/**
 * routes
 * The controller must be in the namespace Test\Controller
 */
$router->namespace("Test")->group("name");

$router->get("/", "Name:home", "name.home");
$router->get("/hello", "Name:hello", "name.hello");
$router->get("/redirect", "Name:redirect", "name.redirect");

/**
 * This method executes the routes
 */
$router->dispatch();

/*
 * Redirect all errors
 */
if ($router->error()) {
    $router->redirect("name.hello");
}
```

#### Named Controller

```php
<?php

class Name
{
    public function __construct($router)
    {
        $this->router = $router;
    }

    public function home(): void
    {
        echo "<h1>Home</h1>";
        echo "<p>", $this->router->route("name.home"), "</p>";
        echo "<p>", $this->router->route("name.hello"), "</p>";
        echo "<p>", $this->router->route("name.redirect"), "</p>";
    }

    public function redirect(): void
    {
        $this->router->redirect("name.hello");
    }
}
```

### Depois:

#### Routes
```php
<?php

use DI\ContainerBuilder;
use DI\Container;
use CoffeeCode\Router\Router;

$builder = new ContainerBuilder();
$builder->addDefinitions([
  Router::class => function (Container $container) {
          return new Router(projectUrl: "https://www.youdomain.com", container: $container);
      }
]);

$container = $builder->build();

$router = $container->get(Router::class);

/**
 * routes
 * The controller must be in the namespace Test\Controller
 */
$router->namespace("Test")->group("name");

$router->get("/", "Name:home", "name.home");
$router->get("/hello", "Name:hello", "name.hello");
$router->get("/redirect", "Name:redirect", "name.redirect");

/**
 * This method executes the routes
 */
$router->dispatch();

/*
 * Redirect all errors
 */
if ($router->error()) {
    $router->redirect("name.hello");
}
```

#### Named Controller

```php
<?php

use CoffeeCode\Router\Router;

class Name
{
    public function __construct(
        protected Router $router
    ) { }

    public function home(): void
    {
        echo "<h1>Home</h1>";
        echo "<p>", $this->router->route("name.home"), "</p>";
        echo "<p>", $this->router->route("name.hello"), "</p>";
        echo "<p>", $this->router->route("name.redirect"), "</p>";
    }

    public function redirect(): void
    {
        $this->router->redirect("name.hello");
    }
}
```

## Creditos

- [Robson V. Leite](https://github.com/robsonvleite) (Dev e autor do projeto)
- [UpInside Treinamentos](https://github.com/upinside) (Time)
- [Todos os Contribuidores](https://github.com/robsonvleite/router/contributors) (Engrenagem que gira o sistema)
- [Gustavo Teixeira](https://github.com/upinside) (Autor da modificação/nova feature)

## Licença

The MIT License (MIT). Please see [License File](https://github.com/robsonvleite/router/blob/master/LICENSE) for more
information.
