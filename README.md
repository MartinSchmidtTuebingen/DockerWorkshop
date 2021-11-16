## TEIL 1:

Container starten: 
```
$docker run -d -p 80:80 docker/getting-started
```

Laufende Container anzeigen:
```
$docker ps
```

PHP-App im Browser aufrufen:
```
localhost(:80)
```

Container stoppen:
```
$docker kill <CONTAINER ID|NAME>
$docker pause <CONTAINER ID|NAME>
$docker stop <CONTAINER ID|NAME> (“sanfte” Art)
```

## TEIL 2:

Hello World in PHP:
```
<?php
echo ‘Hello world’;
```

Image für PHP-Webserver: php:7.4-apache (Images können auf https://hub.docker.com/ gefunden werden)

Mit Container verbinden:
```
$docker exec -it <CONTAINER ID|NAME> bash
```

VIM verwenden: 
```
$vim index.php (Datei öffnen/erstellen)
=> "i" tippen - wechselt in Bearbeitungsmodus => In Datei kopieren, oder direkt eintippen
=> "ESC" (Taste) - verlässt den Editor => ":wq!" - speichert und schließt
```

VIM nachinstallieren:
```
$apt-get -y update && apt-get -y install vim
```

PHP-Datei in Konsole starten: 
```
$php index.php
```

Anleitung Installation Composer:
https://getcomposer.org/download/

Überprüfung:
```
php composer.phar
```

## TEIL 3:
src-Ordner erstellen und Datei dorthin bewegen:
```
$mkdir src && mv index.php src/
```

Container mit Verknüpfung des Filesystems erstellen:
```
$docker run -d -p 80:80 -w /var/www/html -v "$(pwd)/src:/var/www/html" php:7.4-apache
```

Dockerfile:
```
FROM php:7.4-apache
RUN apt-get -y update
RUN apt-get -y install vim
```

Image bauen:
```
$docker build -t docker-workshop:vim .
```

Alle Images anzeigen:
```
$docker images
```

composer.json in src:
```
{ 
  "require": 
  { 
    "vlucas/phpdotenv": "^3"
  }
}
```

Composer-Manuel: https://getcomposer.org/doc/00-intro.md

## TEIL 4:
Datenbank-Dockerfile:
```
FROM mysql:5.7
ENV MYSQL_PASSWORD=example \
    MYSQL_DATABASE=db \
    MYSQL_ROOT_PASSWORD=example \
    MYSQL_ALLOW_EMPTY_PASSWORD=yes
```

Image bauen:
```
$docker build -t <YOUR-DOCKER-NAME>/<DOCKER-REPO> ./dbCreation/
```

Zum Docker-Hub pushen:
```
$docker push <YOUR-DOCKER-NAME>/<DOCKER-REPO>:latest
```

MySQL starten:
```
$mysql -p db
```

Datenbank prüfen:
```
show databases
```

Dockerfile für DB mit eigenen Daten:
```
FROM mysql:5.7
ENV MYSQL_PASSWORD=example \
    MYSQL_DATABASE=db \
    MYSQL_ROOT_PASSWORD=example \
    MYSQL_ALLOW_EMPTY_PASSWORD=yes
COPY db.sql /docker-entrypoint-initdb.d/db.sql
RUN mkdir /var/lib/mysql-no-volume
CMD ["--datadir", "/var/lib/mysql-no-volume"]
```

db.sql:
```
SET SQL_MODE = "NO_AUTO_VALUE_ON_ZERO";
SET AUTOCOMMIT = 0;
START TRANSACTION;
SET time_zone = "+00:00";

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8mb4 */;

DROP TABLE IF EXISTS `test`;
CREATE TABLE `test` (
  `IDX` int(6) UNSIGNED ZEROFILL NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
```

### TEIL 5:
```
docker-compose.yml:
version: '3.1'

services:
  php:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - 80:80
    volumes:
      - ./src:/var/www/html/

  db:
    image: <DB-IMAGE-NAME>
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example

  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
```

Netzwerk starten:
```
$docker-compose up -d
```

PDO Handbuch:
https://www.php.net/manual/en/book.pdo.php

Image löschen:
```
$docker image rm -f <IMAGE-ID|IMAGE-NAME>
```

Alle Images löschen:
```
$docker rmi $(docker images -a -q)
```
