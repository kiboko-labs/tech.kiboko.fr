---
title: "Configuration"
date: 2020-12-01
type : "docs"
---

**Préparation de l'environnement** :

Utilisez la configuration par défaut de l'application installée en mode production.
L'environnement testé doit être le plus proche de l'environnement de production.
Si aucun serveur de messagerie n'est configuré localement, définissez le paramètre 
`mailer_transport` dans `parameters.yml` sur `null`.

**Configuration de Behat**

La configuration de base se trouve dans behat.yml.dist à la racine du projet.
Mais chaque chaque bundle possède aussi sa config dans le dossier du bundle.
Dupliquez donc votre behat.yml.dist en behat.yml (il est déja ajouté dans le .gitignore), et adapter le à votre environnement:

## configuration en local, sans docker

```
yaml
imports:
   - ./behat.yml.dist

 default: &default
     extensions: &default_extensions
         Behat\MinkExtension:
             browser_name: chrome
             base_url: "http://localhost:8010"
```

## configuration avec la stack docker kiboko
```
imports:
    - ./behat.yml.dist

default: &default
    extensions: &default_extensions
        Behat\MinkExtension:
            browser_name: chrome
            base_url: 'http://http'
            default_session: 'first_session'
            sessions:
                second_session:
                    oroSelenium2:
                        wd_host: 'http://localhost:4444/wd/hub'
                        capabilities:
                            browser: chrome
                            extra_capabilities:
                                browser: chrome
                                chromeOptions:
                                    w3c: false
                first_session:
                    oroSelenium2:
                        wd_host: 'http://localhost:4444/wd/hub'
                        capabilities:
                            browser: chrome
                            extra_capabilities:
                                browser: chrome
                                chromeOptions:
                                    w3c: false
                system_session:
                    oroSelenium2:
                        wd_host: 'http://localhost:4444/wd/hub'
                        capabilities:
                            browser: chrome
                            extra_capabilities:
                                browser: chrome
                                chromeOptions:
                                    w3c: false
                320_session:
                    oroSelenium2:
                        wd_host: 'http://localhost:4444/wd/hub'
                        capabilities:
                            extra_capabilities:
                                chromeOptions:
                                    w3c: false
                640_session:
                    oroSelenium2:
                        wd_host: 'http://localhost:4444/wd/hub'
                        capabilities:
                            extra_capabilities:
                                chromeOptions:
                                    w3c: false
```

!!! => Configurer votre parameters.yml avec le search engine en orm et dbal pour les messages

## Installation

### Etat de l'application initiale 

Il faut installer l'appli, sans Elastic, Rabbit, et les données de démo.

```
 bin/console oro:install  --drop-database --user-name=admin --user-email=admin@example.com  \
   --application-url=http://localhost:8010 --user-firstname=John --user-lastname=Doe \
   --user-password=admin  --organization-name=ORO --env=prod --sample-data=n --timeout=3000 \
   --formatting-code=en --language=en
```

### Installation des outils de test en local, sans la stack docker :

Installation de Chromedriver :

Linux : 

```
 CHROME_DRIVER_VERSION=$(curl -sS chromedriver.storage.googleapis.com/LATEST_RELEASE)
 mkdir -p "$HOME/chrome" || true
 wget "http://chromedriver.storage.googleapis.com/${CHROME_DRIVER_VERSION}/chromedriver_linux64.zip" -O "$HOME/chrome/chromedriver_linux64_${CHROME_DRIVER_VERSION}.zip"
 unzip "$HOME/chrome/chromedriver_linux64_${CHROME_DRIVER_VERSION}.zip" -d "$HOME/chrome"
 sudo ln -s "$HOME/chrome/chromedriver" /usr/local/bin/chromedriver
```

Mac  : 

`brew cask install chromedriver`

### Installation des outils de test avec la stack docker :
```
version: '2.2'

services:
  mailcatcher:
    image: schickling/mailcatcher:latest
    ports:
      - ${MAILCATCHER_PORT}:1080

  sql:
    image: postgres:9.6-alpine
    ports:
      - ${SQL_PORT}:5432
    environment:
      - POSTGRES_USER
      - POSTGRES_DB
      - POSTGRES_PASSWORD
      - POSTGRES_ROOT_PASSWORD
    volumes:
      - ./.docker/postgres@9.6/sql/uuid-ossp.sql:/docker-entrypoint-initdb.d/00-uuid-ossp.sql:ro
      - ./.docker/postgres@9.6/custom-sql/:/docker-entrypoint-initdb.d/custom-sql/
      - database:/var/lib/postgresql/data
    restart: on-failure

  http:
    image: nginx:alpine
    volumes:
      - ./.docker/nginx@1.15/config/options.conf:/etc/nginx/conf.d/000-options.conf
      - ./.docker/nginx@1.15/config/reverse-proxy.conf:/etc/nginx/conf.d/default.conf
      - ./:/var/www/html
      - cache:/var/www/html/var/cache:ro
      - assets:/var/www/html/public/bundles:ro
    restart: on-failure
    ports:
      - ${HTTP_PORT}:80
    depends_on:
      - http-worker-prod
      - http-worker-dev
      - http-worker-xdebug

  http-worker-prod:
    image: nginx:alpine
    volumes:
      - ./.docker/nginx@1.15/config/options.conf:/etc/nginx/conf.d/000-options.conf
      - ./.docker/nginx@1.15/config/vhost-prod.conf:/etc/nginx/conf.d/default.conf
      - ./:/var/www/html
      - cache:/var/www/html/var/cache:delegated
      - assets:/var/www/html/public/bundles:delegated
    restart: on-failure
    depends_on:
      - fpm

  http-worker-dev:
    image: nginx:alpine
    volumes:
      - ./.docker/nginx@1.15/config/options.conf:/etc/nginx/conf.d/000-options.conf
      - ./.docker/nginx@1.15/config/vhost-dev.conf:/etc/nginx/conf.d/default.conf
      - ./:/var/www/html
      - cache:/var/www/html/var/cache:ro
      - assets:/var/www/html/public/bundles:ro
    restart: on-failure
    depends_on:
      - fpm

  http-worker-xdebug:
    image: nginx:alpine
    volumes:
      - ./.docker/nginx@1.15/config/options.conf:/etc/nginx/conf.d/000-options.conf
      - ./.docker/nginx@1.15/config/vhost-xdebug.conf:/etc/nginx/conf.d/default.conf
      - ./:/var/www/html
      - cache:/var/www/html/var/cache:ro
      - assets:/var/www/html/public/bundles:ro
    restart: on-failure
    depends_on:
      - fpm-xdebug

  fpm:
    image: kiboko/php:7.2-fpm-blackfire-orocommerce-ee-3.1-postgresql
    user: docker:docker
    volumes:
      - ./:/var/www/html
      - cache:/var/www/html/var/cache
      - assets:/var/www/html/public/bundles
    environment:
      - "I_AM_DEVELOPER_DISABLE_INDEX_IP_CHECK="
      - BLACKFIRE_CLIENT_ID
      - BLACKFIRE_CLIENT_TOKEN
    restart: on-failure

  fpm-xdebug:
    image: kiboko/php:7.2-fpm-xdebug-orocommerce-ee-3.1-postgresql
    user: docker:docker
    volumes:
      - ./:/var/www/html
      - cache:/var/www/html/var/cache
      - assets:/var/www/html/public/bundles
    environment:
      - "I_AM_DEVELOPER_DISABLE_INDEX_IP_CHECK="
    restart: on-failure

  blackfire:
    image: blackfire/blackfire
    environment:
      - BLACKFIRE_SERVER_ID
      - BLACKFIRE_SERVER_TOKEN

  sh:
    build:
      context: .docker/sh@7.2/
    user: docker:docker
    volumes:
      - $HOME/.ssh:/opt/docker/.ssh:cached
      - ./:/var/www/html
      - cache:/var/www/html/var/cache
      - assets:/var/www/html/public/bundles
      - composer:/opt/docker/.composer/:cached
    environment:
      - COMPOSER_AUTH
      - COMPOSER_PROCESS_TIMEOUT
      - BLACKFIRE_CLIENT_ID
      - BLACKFIRE_CLIENT_TOKEN
      - HOME=/var/www/html
    command: [ "sleep", "31536000" ]
    restart: "always"

  sh-xdebug:
    image: kiboko/php:7.2-cli-xdebug-orocommerce-ee-3.1-postgresql
    user: docker:docker
    volumes:
      - $HOME/.ssh:/opt/docker/.ssh:cached
      - ./:/var/www/html
      - cache:/var/www/html/var/cache
      - assets:/var/www/html/public/bundles
      - composer:/opt/docker/.composer/
    environment:
      - COMPOSER_AUTH
      - COMPOSER_PROCESS_TIMEOUT
    command: [ "sleep", "31536000" ]
    restart: "always"

  mq:
    image: kiboko/php:7.2-cli-blackfire-orocommerce-ee-3.1-postgresql
    user: docker:docker
    volumes:
      - ./:/var/www/html
      - cache:/var/www/html/var/cache
      - assets:/var/www/html/public/bundles
    command: [ "bin/console", "oro:message-queue:consume", "--env=prod", "-vv" ]
    restart: "always"

  ws:
    image: kiboko/php:7.2-cli-blackfire-orocommerce-ee-3.1-postgresql
    user: docker:docker
    volumes:
      - ./:/var/www/html
      - cache:/var/www/html/var/cache
      - assets:/var/www/html/public/bundles
    command: [ "bin/console", "gos:websocket:server", "--env=prod", "-vv" ]
    ports:
      - ${WEBSOCKET_PORT}:8080
    restart: "always"

  elasticsearch:
    image: 'docker.elastic.co/elasticsearch/elasticsearch-oss:6.5.4'
    environment:
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - discovery.type=single-node
      - http.port=9200
      - http.cors.allow-origin=http://localhost:${DEJAVU_PORT},http://127.0.0.1:${DEJAVU_PORT},http://192.168.64.4:${DEJAVU_PORT},http://dejavu:${DEJAVU_PORT},http://host.docker.internal:${DEJAVU_PORT}
      - http.cors.enabled=true
      - http.cors.allow-headers=X-Requested-With,X-Auth-Token,Content-Type,Content-Length,Authorization
      - http.cors.allow-credentials=true
    ports:
      - ${ELASTICSEARCH_PORT}:9200
    volumes:
      - elasticsearch:/usr/share/elasticsearch/data
      - ./.docker/elasticsearch/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml
    restart: on-failure

  dejavu:
    image: appbaseio/dejavu
    ports:
      - ${DEJAVU_PORT}:1358

  amqp:
    build:
      context: .docker/rabbitmq@3.6
    ports:
      - ${RABBITMQ_AMQP_PORT}:5672
      - ${RABBITMQ_MANAGER_PORT}:15672
    environment:
      - RABBITMQ_DEFAULT_USER=${RABBITMQ_USER}
      - RABBITMQ_DEFAULT_PASS=${RABBITMQ_PASSWORD}
    restart: on-failure

  redis:
    build:
      context: .docker/redis@5/
    restart: on-failure
    ports:
      - ${REDIS_PORT}:6379

  chrome:
      image: selenium/node-chrome:3.141.59-oxygen
      volumes:
          - /dev/shm:/dev/shm
      depends_on:
          - hub
      environment:
          HUB_HOST: hub

  firefox:
      image: selenium/node-firefox:3.141.59-20200525
      volumes:
          - /dev/shm:/dev/shm
      depends_on:
          - hub
      environment:
          HUB_HOST: hub

  opera:
      image: selenium/node-opera:3.141.59-20200525
      volumes:
          - /dev/shm:/dev/shm
      depends_on:
          - hub
      environment:
          HUB_HOST: hub

  hub:
      image: selenium/hub:3.141.59-20200525
      ports:
          - ${HUB_PORT}::4444
volumes:
  database:
    driver: local
  elasticsearch:
    driver: local
  composer:
    driver: local
    driver_opts:
      type: tmpfs
      device: tmpfs
      o: "size=2048m,uid=1001,gid=1002"
  assets:
    driver: local
    driver_opts:
      type: tmpfs
      device: tmpfs
      o: "size=2048m,uid=1001,gid=1002"
  cache:
    driver: local
    driver_opts:
      type: tmpfs
      device: tmpfs
      o: "size=2048m,uid=1001,gid=1002"

```

### Lancement des outils de tests en local, sans docker

Conditions préalables

- Exécutez ChromeDriver dans un onglet du terminal:

`chromedriver --url-base=wd/hub --port=4444 > /tmp/driver.log 2>&1`

Pour exécuter ChromeDriver en arrière-plan, ajoutez le symbole esperluette (&) à la fin de la ligne, comme dans les exemples suivants:

`chromedriver --url-base=wd/hub --port=4444 > /tmp/driver.log 2>&1 &`

- Lancer un serveur web sur le port spécifié dans el fichier behat.yml

### Execution des tests

Avant de commencer, il est fortement recommandé de vous familiariser avec les arguments et les options de Behat.
Exécutez `bin/behat --help` pour une description détaillée.

Lorsque l'application Oro est installée et est en cours d'exécution, et que ChromeDriver 
est en cours d'exécution, vous pouvez commencer à exécuter les tests.

Vous pouvez utiliser l'une des commandes suivantes.

Exécutez un fichier de test en particulier:

`bin/behat vendor/oro/platform/src/Oro/Bundle/UserBundle/Tests/Behat/Features/login.feature -vvv`

Executer tout les tests d'un bundle:

`bin/behat -s OroUserBundle`

