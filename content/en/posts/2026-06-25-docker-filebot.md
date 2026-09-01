---
title: "New Filebot Docker Image"
faicon: "fa-brands fa-docker"
date: 2026-06-25T18:34:51+01:00
publishDate: 2026-06-25T18:34:51+01:00
images: []
draft: false
tags: [docker,filebot]
summary: A simple docker container for Filebot available over HTTP or VNC.
cover:
    image: "/images/covers/filebot_cropped.jpg"
    alt: "featured image"
    relative: false
---

[Filebot](https://www.filebot.net/) is the ultimate tool for renaming and organizing your movies, TV shows and Anime.

# Why a new Filebot Docker image?

Simple really, I had tried the [Jocelyn Le Sage's image](https://hub.docker.com/r/jlesage/filebot) which has an impressive number of configurable options but I was seeking a simplicity.

I did have it running on my Windows PC but my preference is to have it centralized on my Media Server without requiring a full desktop. Just enough X-Windows to do the job.

---

## Docker

Available on [DockerHub](https://hub.docker.com/r/alandoyle/filebot)
```bash
docker pull alandoyle/filebot
```

---

## Usage

```bash
docker run --name=filebot \
  -d --init \
  -v <MY_DATA_PATH>:/data \
  -v <MY_MEDIA_PATH>:/media \
  -p 80:8080/tcp \
  -p 5900:5900/tcp \
  alandoyle/filebot:latest
```

Docker compose example:

```yaml
---
services:
  filebot:
    image: alandoyle/filebot:latest
    container_name: filebot
    restart: unless-stopped
    init: true
    ports:
      - 80:8080  # If you want to go filebot through a browser
      - 5900:5900  # If you want to use a VNC client instead of the browser
    volumes:
      - /Storage/Incoming:/media
      - ./data:/data
    environment:
      - RESOLUTION=1600x900 # Change resolution (default: 1366x768)
      # - VNC_PASSWORD=8charpwd
    restart: always
    shm_size: "1gb"
```

---

### Ports

| Port       | Description           |
|------------|-----------------------|
| `8080/tcp` | NOVNC HTTP Port       |
| `5900/tcp` | VNC Port              |

---

### Volumes

| Path     | Description                            |
|----------|----------------------------------------|
| `/data`  | path for Filebot configuration files   |
| `/media` | path for Filebot media files           |


## Filebot Licence

If you have a valid Filebot Licence then this file can be copied to the `/data` directory as `.licence`

This file is included in the email confirming registration and is in the following format...

```
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA512

<Licence details here>

-----END PGP SIGNATURE-----
```

Links to buy a [yearly](https://pay.paddle.com/checkout/532439?quantity_variable=0) or [lifetime](https://pay.paddle.com/checkout/534413?quantity_variable=0) licence can be found on the [Filebot download page](https://www.filebot.net/#download).

---


# Conclusion

A simple, lightweight Docker image running "just enough" X-Windows to run [Filebot](https://www.filebot.net/). Perfect for my needs.

Hopefully, somemone else will find this image useful too.
