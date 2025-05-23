---
title: "New SearXNG Docker Image"
faicon: "fa-brands fa-searchengin"
date: 2023-05-27T22:18:55+01:00
publishDate: 2023-05-27T22:18:55+01:00
images: []
draft: false
tags: [docker,searxng]
cover:
    image: "/images/logo/searxng-logo.png"
    alt: "SearXNG Logo"
    relative: false
---

# Why a new Docker SearXNG image?

Simple really. I couldn't find a simple, easy to configure SearXNG Docker image. I wnted to tweak the theme to my own liking and swap out the logos. None of the available Docker mages provided this functionality so I built my own image, based off the [official image](https://hub.docker.com/r/searxng/searxng).

---

# Where is the new image kept?

The image is available on [DockerHub](https://hub.docker.com/r/alandoyle/searxng)
```bash
docker pull alandoyle/searxng
```
---

# How to use this new image?

Using the image is really simple.

It can be run via commandline:

```bash
docker run --name=searxng \
  -d --init \
  -v <MY_CONFIG_PATH>:/etc/searxng \
  -v <MY_TEMPLATE_PATH>:/usr/local/searxng/searx/templates/simple \
  -v <MY_THEME_PATH>:/usr/local/searxng/searx/static/themes/simple \
  -p 8000:8080/tcp \
  alandoyle/searxng:latest
```

Or via Docker compose:

```yaml
version: "3"

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
     - ./searxng/template:/usr/local/searxng/searx/templates/simple
     - ./searxng/theme:/usr/local/searxng/searx/static/themes/simple
```

---

# What ports does the image use?

The SearXNG image only needs one port. Port 8080, the alternative HTTP port.

| Port       | Description           |
|------------|-----------------------|
| `8080/tcp` | HTTP                  |

---

# What Volumes are required?

The 3 paths needed for persistant data and configuration are displayed in the table below.

| Path                                               | Description                           |
|-------------------------------------------------|---------------------------------------|
| `/etc/searxng`                                  | path for SearXNG configuration files |
| `/usr/local/searxng/searx/templates/simple`     | path for SearXNG temolate files          |
| `/usr/local/searxng/searx/static/themes/simple` | path for SearXNG theme files          |


These Volumes are required so all SearXNG settings and themes are kept whenever the Docker container is restarted.

NOTE: On first run these Volumes are populated with the default files required to get SearXNG up and running and ready to begin configuring.

---

# Conclusion

Does this Docker image meet my needs? Well, yes it does, obviously!

Hopefully, somemone else will find this image useful too.

# Updates

2025-05-11 - Updated to latest SearXNG code which fixes Google searches.
