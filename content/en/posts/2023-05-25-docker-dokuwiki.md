---
title: "New Dokuwiki Docker Image"
faicon: "fa-brands fa-docker"
date: 2023-05-25T23:47:05+01:00
publishDate: 2023-05-25T23:47:05+01:00
images: []
draft: false
tags: [docker,dokuwiki]
cover:
    image: "/images/covers/ian-taylor-jOqJbvo1P9g-unsplash_cropped.jpg"
    alt: "featured image"
    relative: false
---

# Why a new Docker Dokuwiki image?

Simple really. I couldn't find a simple, easy to configure Dokuwiki Docker image.

I tried the [linuxserver.io image](https://hub.docker.com/r/linuxserver/dokuwiki) and the [Bitnami image](https://hub.docker.com/r/bitnami/dokuwiki/) but neither felt particularly right and lacked the additional features I wanted.

## Desired features

+ [github-tags-en smileys](https://github.com/davidjimenez75/dokuwiki-smileys-local) by Davidjimenez7
+ The  [Bootstrap 3 theme](https://github.com/giterlizzi/dokuwiki-template-bootstrap3) by Giuseppe Di Terlizzi
+ Clean URL's using .htaccess (via mod_rewrite). 

---

## Initial "success"...

Initially, I followed Logan Marchiones "[The 'best' way to run DokuWiki in Docker](https://loganmarchione.com/2022/03/the-best-way-to-run-dokuwiki-in-docker/)" guide and yes it gave me the ability to add the features I wanted BUT it was a bit TOO manual for my liking, especially as I wanted to use Docker to simplify my setup, so ultimately I created my own image.

I actually ran this in "Production" for over a month, and it works really well, before migrating to my new image.

---

# Where is the new image kept? 

The image is available on [DockerHub](https://hub.docker.com/r/alandoyle/dokuwiki)
```bash
docker pull alandoyle/dokuwiki:latest
```

---

# How to use this new image?

Using the image is really simple. 

It can be run via commandline:

```bash
docker run --name=dokuwiki \
  -d --init \
  -v <MY_CONF_PATH>:/var/www/html/conf \
  -v <MY_DATA_PATH>:/var/www/html/data \
  -p 8000:80/tcp \
  alandoyle/dokuwiki:latest
```

Or via Docker compose:

```yaml
version: "3"

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

---

# What ports does the image use?

The Dokuwiki image only needs one port. Port 80, the standard Apache HTTP port.

| Port     | Description           |
|----------|-----------------------|
| `80/tcp` | HTTP                  |

---

# What Volumes are required?

The 2 paths needed for persistant data and configuration are displayed in the table below.

| Path    | Description                           |
|---------|---------------------------------------|
| `/var/www/html/conf` | path for Dokuwiki configuration files |
| `/var/www/html/data` | path for Dokuwiki data files          |

 
These Volumes are required so all wiki settings and pages are kept whenever the Docker container is restarted.

NOTE: On first run these Volumes are populated with the default files required to get Dokuwiki up and running and ready to begin configuring.

---

# Conclusion

Does this Docker image meet my needs? Well, yes it does, obviously!

Hopefully, somemone else will find this image useful too.

---

```
ADDENDUM:
Initially, I was using the latest PHP 8.2 image as a base but Dokuwiki doesn't seem to work correctly with PHP 8.2 yet so I dropped it back to the latest PHP 7 and all funny behaviour stopped.
```