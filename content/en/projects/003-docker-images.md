---
title: "Docker Images"
url: /projects/docker-images
faicon: "fa-brands fa-docker"
draft: false
ShowToc: true
cover:
    image: "/images/covers/ian-taylor-jOqJbvo1P9g-unsplash_cropped.jpg"
    alt: "featured image"
    relative: false
---

I maintain a small number of personal Docker images on [DockerHub](https://hub.docker.com/u/alandoyle) whioch others may find useful.

# DOKUWIKI

An enhanced Dokuwiki installation with some extra features.

+ [github-tags-en](https://github.com/davidjimenez75/dokuwiki-smileys-local) by Davidjimenez7
+ [Bootstrap 3 theme](https://github.com/giterlizzi/dokuwiki-template-bootstrap3) by Giuseppe Di Terlizzi
+ Clean URLS using mod_rewrite

## Docker

Available on [DockerHub](https://hub.docker.com/r/alandoyle/dokuwiki)
```bash
docker pull alandoyle/dokuwiki
```

## Usage

```bash
docker run --name=dokuwiki \
  -d --init \
  -v <MY_CONF_PATH>:/var/www/html/conf \
  -v <MY_DATA_PATH>:/var/www/html/data \
  -p 8000:80/tcp \
  alandoyle/dokuwiki:latest
```

Docker compose example:

```yaml
services:
  dokuwiki:
   image: alandoyle/dokuwiki:latest
   container_name: dokuwiki
   restart: unless-stopped
   init: true
   ports:
     - "8000:80/tcp"
   volumes:
     - ./dokuwiki/conf:/var/www/html/conf
     - ./dokuwiki/data:/var/www/html/data
```

### Ports

| Port     | Description           |
|----------|-----------------------|
| `80/tcp` | HTTP                  |

### Volumes

| Path    | Description                           |
|---------|---------------------------------------|
| `/var/www/html/conf` | path for Dokuwiki configuration files |
| `/var/www/html/data` | path for Dokuwiki data files          |

---

# SEARXNG

Updated SearXNG Docker image with allows easier customisation.

## Docker

Available on [DockerHub](https://hub.docker.com/r/alandoyle/searxng)
```bash
docker pull alandoyle/searxng
```

## Usage

```bash
docker run --name=searxng \
  -d --init \
  -v <MY_CONFIG_PATH>:/etc/searxng \
  -v <MY_DATA_PATH>:/var/cache/searxng \
  -v <MY_TEMPLATE_PATH>:/usr/local/searxng/searx/templates/simple \
  -v <MY_THEME_PATH>:/usr/local/searxng/searx/static/themes/simple \
  -p 8000:8080/tcp \
  alandoyle/searxng:latest
```

Docker compose example:

```yaml
---
services:
  searxng:
   image: alandoyle/searxng:latest
   container_name: searxng
   restart: unless-stopped
   init: true
   ports:
     - "8000:8080/tcp"
   volumes:
     - ./searxng/config:/etc/searxng
     - ./searxng/data/:/var/cache/searxng
     - ./searxng/template:/usr/local/searxng/searx/templates/simple
     - ./searxng/theme:/usr/local/searxng/searx/static/themes/simple
```

### Ports

| Port       | Description           |
|------------|-----------------------|
| `8080/tcp` | HTTP                  |

### Volumes

| Path                                            | Description                            |
|-------------------------------------------------|----------------------------------------|
| `/etc/searxng`                                  | path for SearXNG configuration files   |
| `/var/cache/searxng`                            | path for SearXNG data/cache files      |
| `/usr/local/searxng/searx/templates/simple`     | path for SearXNG template files        |
| `/usr/local/searxng/searx/static/themes/simple` | path for SearXNG template static files |

---

# TT-RSS MYSQL

{{< error title="MySQL is no longer supported in TT-RSS" >}}
MySQL support was removed from TT-RSS on [28th April 2025](https://git.tt-rss.org/fox/tt-rss.git/commit/?id=4cb8a84df46d46bc325b6638defbdc4dc34151ed)

The latest version of this image in [Github](https://github.com/alandoyle/docker-tt-rss-mysql) and [DockerHub](https://hub.docker.com/r/alandoyle/tt-rss-mysql) is [0e4b8bd6538f3062d34a3a06ab5531c70042de78](https://git.tt-rss.org/fox/tt-rss.git/commit/?id=0e4b8bd6538f3062d34a3a06ab5531c70042de78) which is the *last* version of TT-RSS to support MySQL.
{{< /error >}}

A simple Tiny Tiny RSS image which only supports MySQL with integrated feed updates.

+ Support MySQL server.
+ Built in Feed updating.
+ Built-in TT-RSS updating.

----
## IMPORTANT NOTES

This Docker image has several assumptions/prerequisites which need to be  fulfilled, ignoring them *will* bring failure.

1. This Docker Image is for MySQL _ONLY_.
1. This image is for domains or sub-domains with **/tt-rss/** in the URL. e.g. **http://reader.mydomain.tld**
1. MySQL needs to be installed in a separate Docker container.
1. MySQL needs to be configured and setup **BEFORE** this image is deployed (explained below).
1. If a previous MySQL instance is used and and old TT-RSS instance was using PHP 7.x then a "Data Fix" will need to be applied to the database as this image used PHP 8.1 and generates JSON differently. Failure to "Data Fix" the database could result in duplicate posts appearing in TT-RSS (explained below)

## MySQL Setup

This image _requires_ a database and database user be set up **PRIOR** to bringing up the image.

A new database and user can be created using the _mysql_ command.

e.g. If you're running the MySQL container from the Docker Compose example below you will need to run the following commands to create the database and user.

```bash
docker exec -it <MYSQL_CONTAINER> /bin/bash
```
Once inside the container run the following command to access MySQL.
```bash
mysql -u root -p
```
You will be prompted for the MYSQL_ROOT_PASSWORD (see Docker Compose example)
Once in _mysql_ run the following SQL commands to set up the database and user (see Docker Compose example to match up the values).
```sql
CREATE DATABASE <TTRSS_DB_NAME>;
CREATE USER '<TTRSS_DB_USER>' IDENTIFIED BY '<TTRSS_DB_PASS>';
GRANT ALL ON <TTRSS_DB_NAME>.* TO '<TTRSS_DB_USER>';
FLUSH PRIVILEGES;
\q
```

Now the `tt-rss-mysql` image can be started.

## PHP 7 -> PHP 8 "Data Fix"

**NOTE:** If this is a fresh install of Tiny Tiny RSS then ignore this section.

PHP 7 stores the unique GUID used by each article in the following format:
```
{"ver":2,"uid":"2","hash":"SHA1:2b10b494802dc70e9d9d7676cdef0cf0f9969b78"}
```

PHP 8 stores the unique GUID used by each article in the following format:
```
{"ver":2,"uid":2,"hash":"SHA1:2b10b494802dc70e9d9d7676cdef0cf0f9969b78"}
```

Notice that the "uid" value is quoted with PHP 7 but not PHP 8.

### The fix
To fix a previous MySQL database populated by PHP 7 the following SQL commands need to be used via the _mysql_ commandline tool.
```sql
USE <TTRSS-DATABASE>;

UPDATE ttrss_entries
SET guid = replace(replace(guid,'"uid":"', '"uid":'),'", "hash":', ',"hash":')
WHERE guid LIKE '%"uid":"%"%';
```
----

## Docker

Available on [DockerHub](https://hub.docker.com/r/alandoyle/tt-rss-mysql)
```bash
docker pull alandoyle/tt-rss-mysql
```

## Usage

```bash
docker run --name=tt-rss-mysql \
  -d --init \
  -v <MY_CONF_PATH>:/opt/tt-rss/config.d\
  -v <MY_WEB_PATH>:/var/www/tt-rss\
  -p 8000:80/tcp \
  alandoyle/tt-rss-mysql:latest
```

Docker compose example:

```yaml
services:
  mysql:
    image: mysql:8.0
    container_name: mysql
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: SecureSecretPassword
    volumes:
      - ./mysql/data:/var/lib/mysql
  tt-rss-mysql:
   image: alandoyle/tt-rss-mysql:latest
   container_name: tt-rss-mysql
   restart: unless-stopped
   init: true
   ports:
     - "8000:80/tcp"
    volumes:
      - ./tt-rss/web:/var/www/tt-rss
      - ./tt-rss/config:/opt/tt-rss/config.d
    environment:
      TTRSS_SELF_URL_PATH: https://reader.mydomain.tld
      TTRSS_DB_HOST: mysql
      TTRSS_DB_USER: ttrss_user
      TTRSS_DB_NAME: ttrss_database
      TTRSS_DB_PASS: ttrss_password
      TTRSS_DB_PORT: 3306
      TTRSS_FEED_UPDATE_CHECK: 600
```

### Ports

| Port     | Description           |
|----------|-----------------------|
| `80/tcp` | HTTP                  |

### Volumes

| Path    | Description                           |
|---------|---------------------------------------|
| `/var/www/tt-rss` | path for tt-rss web files |
| `/opt/tt-rss/config.d` | path for tt-rss configuration files          |
