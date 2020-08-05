---
title: "Créer une application frontend sur OroPlatform"
date: 2020-05-20
type : "docs"
---

OorPlatform fournit un environnement complet pour créér des applications métier dédiées à une équipe interne d'une entreprise.
Il ne fournit cependant pas d'interface pubique à destination de tiers, à l'exception notable de l'interface *OroCommerce Store Front*.

Nous allons créer un socle d'application OroPlatform qui disposera d'un accès client. Par défaut, une application OroPlatform dispose 
d'une seule application déclarée, nommée `default`. C'est sur cette application que l'on développe des interfaces métier, nous allons 
chercher à créer une seconde application nommée `helpdesk_portal`

Architecture
---

Nous allons nous appliquer à créer cette application sur OroPlatform 4.1 Community Edition et Symfony 4.4.

Nous tenterons de respecter une organisation de notre code en DDD - *Domain Driven Development*

```
src/
 └ HelpDeskPortal/
    ├	Bundle/
    │  ├ DependencyInjection/
    │  │  │ Configuration.php
    │  │  └ KibokoHelpDeskPortalExtension.php
    │  ├ Resources/
    │  │  └ config/
    │  │     └ oro/
    │  └ KibokoHelpDeskPortalBundle.php
    ├ Domain/
    │  └ Frontend/
    └ Infrastructure/
       ├ Persistence/
       │  └ Doctrine/
       │     └ Repository/
       └ Symfony/
          ├ DependencyInjection/
          ├ Request/
          ├ Router/
          └ Session/
```

Créer le projet
---

Nous allons partir d'un projet OroPlatform CE 4.1 vierge.

```
git clone git@github.com:oroinc/platform-application.git helpdesk-application
cd helpdesk-application
git checkout 4.1.2
rm -rf .git
git init
git add -A .
git commit -m "Initial commit from git@github.com:oroinc/platform-application.git atr version 4.1.2"
composer install
```

Routage
---

Dans le but de simplification, nous allons activer l'auto-wiring sur notre composant de routage :

### Autowiring

```yaml
services:
  Kiboko\HelpDeskPortal\Infrastructure\Symfony\Router\:
    resource: '../src/HelpDeskPortal/Infrastructure/Router'
    autowire: true
    autoconfigure: true
```

### Ajouter un préfixe aux routes OroPlatform

Pour la première étape, nous allons ajouter un préfixe `admin/` à nos routes standard OroPlatform, comme le fait OroCommerce.

Nous utiliserons le paramètre de configuration `%web_backend_prefix%` pour définir facilement cette valeur. Nous profiterons 
que OroCommerce ait déjà défini ce paramètre.

Nous alons utiliser un event listener qui va retraiter les déclarations de routes, tel que :

```php
<?php declare(strict_types=1);

namespace Kiboko\HelpDeskPortal\Infrastructure\Symfony\Router\EventListener;

use Oro\Bundle\ApiBundle\ApiDoc\RestRouteOptionsResolver;
use Oro\Bundle\DistributionBundle\Event\RouteCollectionEvent;
use Symfony\Component\Routing\Route;

class RouteCollectionListener
{
    const OPTION_FRONTEND = 'helpdesk_portal';

    private string $prefix;

    public function __construct(string $prefix)
    {
        $this->prefix = trim(trim($prefix), '/');
    }

    public function onCollectionAutoload(RouteCollectionEvent $event): void
    {
        if ('' === $this->prefix) {
            return;
        }

        /** @var Route $route */
        foreach ($event->getCollection()->getIterator() as $route) {
            if ($route->hasOption(self::OPTION_FRONTEND) && $route->getOption(self::OPTION_FRONTEND)) {
                continue;
            }

            if (!$this->hasPrefix($route->getPath())) {
                $route->setPath($this->prefix . $route->getPath());
            }

            if ($route->hasOption(RestRouteOptionsResolver::OVERRIDE_PATH_OPTION)) {
                $overridePath = $route->getOption(RestRouteOptionsResolver::OVERRIDE_PATH_OPTION);
                if (!$this->hasPrefix($overridePath)) {
                    $route->setOption(
                        RestRouteOptionsResolver::OVERRIDE_PATH_OPTION,
                        $this->addPrefix($overridePath)
                    );
                }
            }
        }
    }

    private function hasPrefix(string $path): bool
    {
        $prefix = $this->prefix . '/';

        return
            0 === strpos($path, $prefix)
            || 0 === strpos($path, '/' . $prefix);
    }

    private function addPrefix(string $path): string
    {
        if (0 === strpos($path, '/')) {
            return '/' . $this->prefix . $path;
        }

        return $this->prefix . '/' . $path;
    }
}
```

Et la déclaration de service qui lui correspond :

```yaml
services:
  Kiboko\HelpDeskPortal\Infrastructure\Symfony\Router\EventListener\RouteCollectionListener:
    arguments:
      - '%web_backend_prefix%'
    tags:
      - { name: kernel.event_listener, event: oro_distribution.route_collection.expose, method: onCollectionAutoload, priority: 120 }
      - { name: kernel.event_listener, event: oro_distribution.route_collection.autoload, method: onCollectionAutoload, priority: 120 }
    lazy: true
```

### Extracteur de routes frontend

Pour pouvoir exporter les routes javascript, nous avons besoin d'extraire les routes du portail Helpdesk.

```php
<?php

namespace Kiboko\HelpDeskPortal\Infrastructure\Symfony\Router;

use FOS\JsRoutingBundle\Extractor\ExposedRoutesExtractor;
use Symfony\Component\Routing\Route;

/**
 * Expose routes which marked with option `helpdesk_portal: true`.
 */
class PortalExposedRoutesExtractor extends ExposedRoutesExtractor
{
    /**
     * {@inheritdoc}
     */
    public function isRouteExposed(Route $route, $name)
    {
        return parent::isRouteExposed($route, $name) &&
            $route->hasOption('helpdesk_portal') &&
            $route->getOption('helpdesk_portal');
    }

    /**
     * {@inheritdoc}
     */
    public function getCachePath($locale)
    {
        $path = parent::getCachePath($locale);
        $fileName = basename($path);
        $fileName = 'helpdesk_portal_'.$fileName;

        return dirname($path) . DIRECTORY_SEPARATOR . $fileName;
    }
}
```

Ainsi que les services nécessaires à l'extraction de ces routes Javascript :

```yaml
services:
  Kiboko\HelpDeskPortal\Infrastructure\Symfony\Router\PortalExposedRoutesExtractor:
    arguments:
      - '@router'
      - []
      - '%kernel.cache_dir%/oro_data'
      - '%kernel.bundles%'

  kiboko_help_desk_portal.js_routing_controller:
    class: '%fos_js_routing.controller.class%'
    public: true
    arguments:
      - '@fos_js_routing.serializer'
      - '@Kiboko\HelpDeskPortal\Infrastructure\Symfony\Router\PortalExposedRoutesExtractor'
      - '%fos_js_routing.cache_control%'
      - '%kernel.debug%'
```

Nous aurons également besoin d'exposer une route supplémentaire pour le service de contrôleur `iboko_help_desk_portal.js_routing_controller` que nous avons créé plus haut :

```yaml
kiboko_help_desk_portal_js_routing_js:
  path: "/media/js/frontend_routes.{_format}"
  defaults: { _controller: kiboko_help_desk_porta.js_routing_controller:indexAction }
  requirements:
    _format: json
  options:
    helpdesk_portal: true
```