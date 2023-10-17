---
title: "Setup GOGS using MySQL in Docker"
faicon: "fa-brands fa-git"
date: 2023-08-09T14:36:31+01:00
publishDate: 2023-08-09T14:36:31+01:00
images: []
draft: false
tags: [gogs,git,docker]
showToc: true
cover:
    image: "/images/logo/gogs-logo.png"
    alt: "GOGS"
    relative: false
---

# What is GOGS?

The [GOGS](https://gogs.io) project aims to build a simple, stable and extensible self-hosted Git service that can be set up in the most painless way.

## Features

- User dashboard, user profile and activity timeline.
- Access repositories via SSH, HTTP and HTTPS protocols.
- User, organization and repository management.
- Repository and organization webhooks, including Slack, Discord and Dingtalk.
- Repository Git hooks, deploy keys and Git LFS.
- Repository issues, pull requests, wiki, protected branches and collaboration.
- Migrate and mirror repositories with wiki from other code hosts.
- Web editor for quick editing repository files and wiki.
- Jupyter Notebook and PDF rendering.
- Authentication via SMTP, LDAP, reverse proxy, GitHub.com and GitHub Enterprise with 2FA.
- Customize HTML templates, static files and many others.
- Rich database backend, including PostgreSQL, MySQL, SQLite3 and TiDB.
- Have localization over 31 languages.

---

# Why run a self-hosted Git service when you have one or more Github accounts?

Simple really. There are some things which need version control but are too sensitive, use case specific or simply too embarassing (we all have code like that) to publish to Github or any public Git service.

Personally, I use GOGS to store my Ansible setup (including username/passwords) to simplify deployments. I also use it for "Internal" projects i.e. scripts, software that are solely of use to me. Honestly, I have no embarassing code :winking_face_with_tongue:

Seeing as I run all my local services in Docker, inside an LXC container on Proxmox, running GOGS as a Docker container made sense. I also have a MySQL database for various other projects so reusing that also made sense. My biggest issue was that most of the tutorials were based around Postgres SQL server.

---

# Basic Docker Compose file

``` yaml
---
version: '3.9'
services:

  ## MySQL 8.0
  mysql:
    image: mysql:8.0
    container_name: mysql
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: SECRET_MYSQL_ROOT_PASSWORD
    volumes:
      - ./mysql/data:/var/lib/mysql
    networks:
      customnetwork:
        ipv4_address: 10.1.1.2

  ## GOGS
  gogs:
    container_name: gogs
    image: gogs/gogs:latest
    restart: unless-stopped
    ports:
      - 80:3000
    volumes:
      - ./gogs/data:/data
    environment:
      - RUN_CROND=true
      - BACKUP_INTERVAL=1d
      - BACKUP_RETENTION=14d
    depends_on:
      - mysql
    networks:
      customnetwork:
        ipv4_address: 10.1.1.3

networks:
  customnetwork:
    ipam:
      config:
        - subnet: 10.1.1.0/24
```

---

# MySQL Setup

![Hard drive](/images/blog/benjamin-lehman-GNyjCePVRs8-unsplash_cropped.jpg)

MySQL needs to be set up with a **gogs** database and user prior to running through the GOGS installation process.

NOTE: Please choose a strong, secure *root* password above.

## Create database and user

To create the database you need to connect to the MySQL Docker container and run some SQL commands.

``` bash
docker exec -it mysql /bin/bash
```

Form there execute...

``` bash
mysql -u root -p
```

Use the *SECRET_MYSQL_ROOT_PASSWORD* from the *docker-compose.yml* file.

Once inside the MySQL commandline client run the following SQL commands...

``` sql
SET GLOBAL innodb_file_per_table = ON;
DROP DATABASE IF EXISTS gogs;
CREATE DATABASE IF NOT EXISTS gogs CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
CREATE USER 'gogs'@'%' IDENTIFIED BY 'StrongPassword';
GRANT ALL PRIVILEGES ON gogs.* TO 'gogs'@'%';
FLUSH PRIVILEGES;
\q
```

Please choose a proper *StrongPassword* for the **gogs** user.

---

# Setup GOGS

![Source Code](/images/blog/chris-ried-ieic5Tq8YMk-unsplash_cropped.jpg)

Connect to the host IP address on the port exposed by the *docker-compose.yml* file, in the example above it's port 80, and you'll be presented with the **First-time Run** page.

![First-time Run page](/images/blog/gogs-first-time-run-page.jpg)

## Database Settings

| Setting       | Value            |
|---------------|------------------|
| Database Type | MySQL            |
| Host          | mysql:3306       |
| User          | gogs             |
| Password      | *StrongPassword* |
| Database Name | gogs             |

## Application General Settings

I only change some of these settings.
I like to persist the log files hence changing the *Log Path*

| Setting          | Example Values            |
|------------------|---------------------------|
| Application Name | My Private Git Repository |
| Application URL  | https://mygitrepo.tld     |
| Log Path         | /data/log                 |
| Default Branch   | main                      |

> NOTE: If specifying an HTTPS Application URL then GOGS must be hosted behind an HTTPS Proxy like [Nginx Proxy Manager](https://nginxproxymanager.com/), [Traefik](https://traefik.io/traefik/), [HAProxy](https://www.haproxy.org/), [Cloudflare Zero Trust](https://www.cloudflare.com/zero-trust/), etc. as it doesn't directly support HTTPS connections.

## Optional Settings

I like to fill in the **Email Service Settings**.

*SMTP Host* is in the format of <SMTP Hostname>:<SMTP Port> e.g. `smtp.gmail.com:587`

Under **Server and Other Services Settings** I tend to only check 2 options, *Disable Self-registration* and *Enable Require Sign In to View Pages* if I'm setting up an internal, private Git server.

And finally under **Admin Account Settings** I *always* set up an Administrative account that **WONT** be used to commit changes to any repositories. Later I'd create a non-Adminastrative user to do all the Repository creation and commits to Git.

> NOTE: If *Enable Require Sign In to View Pages* is enabled then the Docker container **WILL** need to be restarted for it to take effect.

Once finished click the blue **Install GOGS** button. This will now complete the installation and log you in as the Administrative User if one was set up.

From here you'll be able to create non-Administrative users and start using your Git server.

---

# Conclusion

I've been running an internal GOGS server for a number of years now and while it doesn't contain all the features bigger platforms like Github, Gitlab and Forgejo possess, it's small, compact nature fits the bill (for me) perfectly.

Honestly, some of the settings I've outlined above are personal preferences but it's how I like to set up a personal, internal and private Git Server. GOGS is fantastic, lightweight and similar enough to Github that using it is really easy.

Hopefully someone will find this useful and give GOGS a go as their own personal Git Server.