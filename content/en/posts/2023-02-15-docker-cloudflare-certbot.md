---
title: "Certbot using Cloudflare DNS in Docker"
date: 2023-02-15T15:25:12Z
publishDate: 2023-02-15T15:25:12Z
faicon: "fa-brands fa-docker"
images: []
draft: false
tags: [docker,updates,cloudflare,hugo]
aliases: [/blog/docker-cloudflare-certbot/]
cover:
    image: "/images/blog/le-logo-wide.png"
    alt: "Lets Encrypt"
    relative: false
---

# Encrypt all the things!

Let's Encrypt will issue you free SSL certificates (including wildcard sub-domain certificates), but you have to verify you control the domain, before they issue the certificates.

The 2 major ways of proving control over the domain:

1. Create a specific page on your webserver that they can reach.
2. Create a temporary DNS TXT record.

I went with option #2 as this meant I didn't need to deploy non-website files/folders to use Certbot.

---

# Cloudflare DNS for the win!

![Cloudflare](/images/blog/CF_logo_horizontal_blktype.png)

I already use Cloudflare for my DNS needs, as well as a CDN and deploying this Hugo site through Cloudflare Pages so when I discovered that they have an API that allows the temporary DNS TXT records to be created/deleted vias Certbot it was a no-brainer to run with Cloudflare DNS and Certbot. All you need is a Cloudflare API Token for either the domain or entire account.

---

# Cloudflare API Tokens

To allow Certbot to interact with Cloudflare DNS we need to generate a Cloudflare API Token which is really simple.

1. Login into the [Cloudflare dashboard](https://dash.cloudflare.com/profile/api-tokens/), go to **My Profile** > **API Tokens**.

![Cloudflare API Tokens](/images/blog/cloudflare-api-tokens.jpg)

2. Click on the **Create Token** button and choose the **Use template** button beside **Edit zone DNS**.

![Cloudflare API Tokens - Edit zone DNS](/images/blog/cloudflare-api-tokens-edit-zone-dns.jpg)

3. Give the Token a proper name. In my case I set it to **Certbot** (see below).

   Depending on your use case the API Token can be restricted to just alter the DNS records of a single domain or all domains associated with an account.

   In order to limit the API Token to a single domain choose the **Specific zone** dropdown under **Zone Resources** (as highlighted below) and the select the domain from the next dropdown. Alternatively to create an API Token which can update ALL domains change the **Specific zone** dropdown under **Zone Resources** to **All zones**.

   Under **TTL**, select Start/End dates, or leave untouched for no expiration of these permissions.

   Click **Continue to summary** to continue.

![Cloudflare API Tokens - Settings](/images/blog/cloudflare-api-tokens-settings.jpg)

4. If you're happy with your settings then click the **Create Token** button.

   NOTE: Once generated, copy the API token and treat it like a password. If you lose the API Token you will not be able to recover it in the Cloudflare Dashboard so you will need to delete it and generate a new API Token.

![Cloudflare API Tokens - Final Step](/images/blog/cloudflare-api-tokens-final-step.jpg)

## Testing the API Token

When the API Token is created Cloudflare will display a simple ***curl*** command that can be used to verify the API Token:

```bash
curl -X GET "https://api.cloudflare.com/client/v4/user/tokens/verify" \
 -H "Authorization: Bearer XPIM4QJ3L8BCNS4ZFDBRDYX7JZ0VC3Y6MTJD0IQW2M" \
 -H "Content-Type:application/json"
{"result":{"id":"2574cfa00d06b4d6b74d9b720852fe7f","status":"active"},"success":true,"errors":[],"messages":[{"code":10000,"message":"This API Token is valid and active","type":null}]}
```

---

# Using the Cloudflare API Token to generate an SSL Certificate

To simplify matters I've created a [Docker container](https://github.com/alandoyle/docker-cloudflare-certbot) which when installed on a server creates a set of commands to Generate, Test and Renew certificates for that server.

NOTE: This Docker container assumes a single API Token is used for all domains which it'll generate certificates for.

To install simply run the following command at a terminal prompt on Linux.

```bash
sudo bash -c "$(curl -sSfL https://raw.githubusercontent.com/alandoyle/docker-cloudflare-certbot/main/install)"
```

The installation process will request the API Token generated earlier and set up a single-shot Docker container which will then be used to generate and renew certificates. The installation process will also set up a cron entry for the root user so certificate renewals will happen automatically.

Once installed the script will add 3 new commands to */usr/local/bin*

  - gen-cert
  - renew-certs
  - test-certbot

## gen-cert

Generate a Lets Encrypt cetrificate.

**Note:** ***-w*** adds wildcard subdomains to the certificate.

```bash
Usage:
   sudo gen-cert domain.com
   sudo gen-cert -w domain.com
```

## renew-certs

Renews all Lets Encrypt cetrificates on the system.

**Note:** This command is called by cron to renew all the certificates automatically.

```bash
Usage:
   sudo renew-certs
```

## test-certbot

This command tests the configuration to ensure we can generate Lets Encrypt cetrificates on the system.

**Note:** This command **MUST** be called with a domain which the API Token has access to for the test to succeed.


```bash
Usage:
   sudo test-certbot domain.com
```

---

# Conclusion

All-in-all Lets Encrypt's Certbot and Cloudflares DNS API Token support makes generating SSL certificates really easy, so now all my systems use proper SSL certificates, both internal and external systems.

As an additional note, the reason I chose Docker instead of certbot from Ubuntu's APT or Snap Store is because I wanted to always use the latest official versions so I don't suddenly lose connectivity to my servers because the APT or Snap version is out of date and incompatible, which has happened in the past.
