---
title: "New Tiny Tiny RSS Docker Image"
faicon: "fa-solid fa-square-rss"
date: 2023-06-03T12:19:27+01:00
publishDate: 2023-06-03T12:19:27+01:00
images: []
draft: false
tags: [docker,tt-rss]
cover:
    image: "/images/logo/ttrss-logo.png"
    alt: "Tiny Tiny RSS & MySQL Logos"
    relative: false
---

{{< error title="MySQL is no longer supported in TT-RSS" >}}
MySQL support was removed from TT-RSS on [28th April 2025](https://git.tt-rss.org/fox/tt-rss.git/commit/?id=4cb8a84df46d46bc325b6638defbdc4dc34151ed)

The latest version of this image in [Github](https://github.com/alandoyle/docker-tt-rss-mysql) and [DockerHub](https://hub.docker.com/r/alandoyle/tt-rss-mysql) uses a Git Hash of [0e4b8bd6538f3062d34a3a06ab5531c70042de78](https://git.tt-rss.org/fox/tt-rss.git/commit/?id=0e4b8bd6538f3062d34a3a06ab5531c70042de78) which is the *last* version of TT-RSS to support MySQL.
{{< /error >}}

# Why a new Docker Tiny Tiny RSS image?

Simple really. I couldn't find a simple, easy to configure [Tiny Tiny RSS](https://tt-rss.org/) Docker image with MySQL support.

Originally, back in 2013, when I first set Tiny Tiny RSS it was installed straight onto the root partition of a VPS, later I migrated to an LXD container but recently I've been migrating all my LXD containers to Docker containers. The problem was that Postgres is the prefered database for Tiny Tiny RSS and convertiong between MySQL and Postgres essentailly boils down to dropping the MySQ daabase and starting from scratch in Postgres. However, I have more than 16,000 Starred Articles which I didn't want to lose (but I do need to moderate) so I created a MySQL flavoured version of the Tiny Tiny RSS Docker image.

## Desired features

+ Support MySQL server.
+ Built in Feed updating.
+ Built-in TT-RSS updating.

---

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


# Where is the new image kept?

The image is available on [DockerHub](https://hub.docker.com/r/alandoyle/tt-rss-mysql)
```bash
docker pull alandoyle/tt-rss-mysql:latest
```

---

# How to use this new image?

Using the image is really simple.

It can be run via commandline:

```bash
docker run --name=tt-rss-mysql \
  -d --init \
  -v <MY_CONF_PATH>:/opt/tt-rss/config.d\
  -v <MY_WEB_PATH>:/var/www/tt-rss\
  -p 8000:80/tcp \
  alandoyle/tt-rss-mysql:latest
```

Or via Docker compose:

```yaml
version: "3"

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

---

# What ports does the image use?

The Tiny Tiny RSS image only needs one port. Port 80, the standard HTTP port.

| Port     | Description           |
|----------|-----------------------|
| `80/tcp` | HTTP                  |

---

# What Volumes are required?

The 2 paths needed for persistant data and configuration are displayed in the table below.

| Path    | Description                           |
|---------|---------------------------------------|
| `/var/www/tt-rss` | path for tt-rss web files |
| `/opt/tt-rss/config.d` | path for tt-rss configuration files          |

These Volumes are required so all settings are kept whenever the Docker container is restarted.

NOTE: On first run these Volumes are populated with the default files required to get Tiny Tiny RSS up and running and ready to begin configuring.

---

# Conclusion

Does this Docker image meet my needs? Well, yes it does, obviously!

Hopefully, somemone else will find this image useful too.

{{< notification title="Updates" >}}
  * 2025-05-23 - Changed Docker Image to use last known version of TT-RSS which supports MySQL.
{{< /notification >}}
