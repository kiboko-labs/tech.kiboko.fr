---
title: "Importer un dump SQL avec Docker"
date: 2020-03-06
type : "docs"
---

Scénario : Nous avons un dump que nous voulons importer sur une base vierge pour avoir une appli qui tourne avec de la donnée.

On peut copier un fichier du local au container Docker dans le dossier `/tmp/` :
```bash
docker cp dump.sql mycontainer:/tmp/dump.sql
```

L'inverse est possible aussi (copier du container Docker au host) :
```bash
docker cp mycontainer:/tmp/dump.sql /chemin/dump.sql
```

On se connecte au container SQL de docker :
```bash
docker exec -ti le-container_mysql bash
``````

On lance la commande avec l'option -v ( pour voir le dump avec du "verbose" ) depuis le container SQL :

`mysql` :
```bash
mysql -v —batch -h{host} -u{user} -p nom-de-la-base-de-donnée < /tmp/bdd.sql
``````

`postgresql` :
```bash
psql -U {user} -d {database} -f tmp/dump.sql
 ``````

Short one :)

## Backup

```bash
docker exec CONTAINER /usr/bin/mysqldump -u root --password=root DATABASE > backup.sql
```

## Restore
```bash
cat backup.sql | docker exec -i CONTAINER /usr/bin/mysql -u root --password=root DATABASE
```