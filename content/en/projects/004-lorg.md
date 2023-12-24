---
title: "lorg"
description: "(Pronounced Lur-Ugh) A framework and javascript free privacy respecting Google Text/Image Proxy originally based on a cut-down version of LibreX but now evolved into simple, lightweight API driven Meta Search Engine."
url: /projects/lorg
draft: false
faicon: fab fa-php
ShowToc: true
cover:
    image: "/images/covers/agence-olloweb-d9ILr-dbEdg-unsplash_cropped.jpg"
    alt: "featured image"
    relative: false
---

## What is it?

[(Pronounced Lur-Ugh - An Irish word meaning "Track, trace; seek, search for." )](https://www.teanglann.ie/en/fgb/lorg)

*lorg* is a framework and javascript free privacy respecting Invidious/Google Text/Image Proxy based on a simple, lightweight API driven Meta Search Engine.

It uses Google to return Text and Image searches, while it's Youtube search results are handled by an [Invidious](https://invidious.io/) instance.

A [demo site](https://search.lorg.dev) has been set up. This site is _NOT_ meant for daily usage and is, deliberately, rate-limited.

---

## Features

*lorg* has a number of interesting features.

 * *lorg* is incredibly lightweight.
 * All Text searches have the tracking removed and a clean URL returned.
 * It can be configured so the Image Search returns a link to the image, rather than the page which includes the image.
 * It also has a simple API whereby and `ìnstances.json`file can be configured with a number of URLS to other *lorg* servers thus spreading the search load.
 * It normally uses a random User Agent but can be configured to send through the connection user Agent or a pre-configured User Agent.

---

## Special searches

 * All searches with one or two words are automatically checked against Wikipedia. (e.g. [Star Wars](https://search.lorg.dev/search?q=star+wars&t=0&p=0))
 * Currency Convertor (provided by [Money Convert](https://moneyconvert.net/)) (e.g. '[12 GBP to EUR](https://search.lorg.dev/search?q=12+GBP+to+EUR&t=0&p=0)')
 * Current weather (provided by [OpenMeteo](https://open-meteo.com/)) (e.g. 'weather')
 * Your External IP (e.g. '[my ip](https://search.lorg.dev/search?q=my+ip&t=0&p=0)')
 * Your User Agent (e.g. '[my user agent](https://search.lorg.dev/search?q=my+user+agent&t=0&p=0)')
 * Word definition (provided by [Free Dictionary API](https://dictionaryapi.dev/)) (e.g. '[run meaning](https://search.lorg.dev/search?q=run+meaning&t=0&p=0)')

---

## Example Config File

```php
<?php

return [
    "base_url"                 => "https://search.mydomain.tld", // This doesn't technically need to be set but doesn't hurt :)
    'api_redirect'             => false,
    'api_redirect_url'         => '',
    'api_only_forced'          => false,
    'api_disabled'             => true,

    "footer_message"           => "This is a Demo Server.",

    "google_domain"            => "com",
    "google_language_site"     => "en",
    "google_language_results"  => "en",
    "google_number_of_results" => "20",

    "opensearch_title"         => "lorg",
    "opensearch_description"   => "My private lorg instance.",
    "opensearch_encoding"      => "UTF-8",
    "opensearch_long_name"     => "lorg Metasearch Engine",

    'accept_langauge'          => 'en-GB',

    'template'                 => 'lorg',
    'hide_templates'           => true,

    'use_client_ua'            => false,
    'use_specific_ua'          => '',
    'link_google_image'        => false,
    'use_image_proxy'          => true,
    'minify_output'            => true,
    'include_local_instance'   => true,

    "wikipedia_language"       => "en",

    "timezone"                 => "Europe/London",

    "invidious_url"            => "https://y.com.sb",
];
```

---

## Example Instances.JSON file

```json
{
    "instances": [
          {
              "Name" : "Search Page (API1)",
              "URL" : "https://mydomain.tld/api"
          },
          {
              "Name" : "Search Page (API2)",
              "URL" : "https://lorg.mydomain2.tld/api"
          },
          {
              "Name" : "Search Page (API3)",
              "URL" : "https://search.mydomain.tld/api"
          }
    ]
}
```

---

## Docker Setup (Recommended)

Available on [DockerHub](https://hub.docker.com/r/alandoyle/lorg) 
```bash
docker pull alandoyle/lorg
```

### Usage

```bash
docker run --name=lorg \
  -d --init \
  -v <MY_CONFIG_PATH>:/var/www/lorg/config \
  -v <MY_TEMPLATE_PATH>:/var/www/lorg/template \
  -p 8000:80/tcp \
  alandoyle/lorg:latest
```

Docker compose example:

```yaml
version: "3"

services:
  lorg:
   image: alandoyle/lorg:latest
   container_name: lorg
   restart: unless-stopped
   init: true
   ports:
     - "8000:80/tcp"
   volumes:
     - ./lorg/config:/var/www/lorg/config
     - ./lorg/template:/var/www/lorg/template
```

### Ports

| Port     | Description           |
|----------|-----------------------|
| `80/tcp` | HTTP                  |

### Volumes

| Path                     | Description                       |
|--------------------------|-----------------------------------|
| `/var/www/lorg/config`   | path for lorg configuration files |
| `/var/www/lorg/template` | path for lorg template files      |

---

## Screenshot

![lorg screenshot](/images/blog/lorg-screenshot.jpg)
