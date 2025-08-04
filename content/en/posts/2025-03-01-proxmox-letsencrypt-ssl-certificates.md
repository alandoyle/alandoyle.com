---
title: "Proxmox - LetsEncrypt SSL Certificates"
description: ""
author:
email:
faicon: fa-solid fa-lock
date: 2025-03-01T14:06:47+01:00
publishDate: 2025-03-01T14:06:47+01:00
images: []
draft: false
showToc: true
tags: [proxmox,cloudflare,letsencrypt,acme]
summary: Replacing the default self-signed SSL certifiacte with a legit Lets-Encrypt certificate is really simple in Proxmox. It's also kept up-to-date automatically once set up.
cover:
    image: "/images/logo/proxmox-full-lockup-color.png"
    alt: "Proxmox"
    imageurl: "https://www.proxmox.com"
    showcredits: false
    relative: false
    hidden: false
---

# Why?

The "why" is simple; I prefer valid HTTPS certificates on all my HTTPS interfaces.

## Is it necessary?

No, from a security perspective. A self-signed certifcate will give youi a secure, encrypted connection and so no data is transmitted in plain text over the network. However, a valid SSL certificate adds an layer of trust and gets rid of the annoying browser security warning associated with self-signed certificates.

![Self-signed Warning](/images/blog/self-signed-warning.jpg)

---

# Requirements for an ACME (Lets Encrypt) SSL certificate in Proxmox

Before proceeding with getting ACME SSL certificates within Proxmox, ensure that you have the following:

  - A working Proxmox server (obviously)
  - Domain Name - I use [NameCheap](https://namecheap.com) for purchasing domains.
  - A DNS Provider - I use [Cloudflare](https://cloudflare.com) (its free).

In this guide, I'll be using Cloudflare as the DNS provider and so we'll also need the following:

  - Your Cloudflare Account ID
  - Your Cloudflare API Token

---

# Create a DNS A Record

Using Cloudflare as your DNS name server, we will need to create a DNS A record that points to your Proxmox server's IP address.

To do this, login to your Cloudflare dashboard and under **DNS** tab, create a new *DNS Type A* record by clicking the **Add Record** button:

![Cloudflare DNS: Add Record](/images/blog/cloudflare-dns-add-record.jpg)

Then fill out the required details and click **Save**

![Cloudflare DNS: Save Record](/images/blog/cloudflare-dns-save-record.jpg)

### Required details
  - Name (e.g. proxmox)
  - IPv4 address (e.g 192.168.1.9 for domain to only work on the local network and fail outside your home/lab)
  - Proxy Status (e.g MUST be DISABLED for internal IP address)

In the above example, my Proxmox server will be available at **proxmox.alandoyle.com**, which points to the internal IP address **192.168.1.9** and will only be available on my home network.

Usually, Cloudflare DNS records usually propagate very quickly (<5 min). But sometimes it can be longer (up to 24 hours) for DNS servers around the world to cache the information. This is why we are start with this the DNS record first.

---

# Get your Cloudflare Account ID

Proxmox ACME SSL certificate creation needs your Cloudflare Account ID and an API Token. To get the *Account ID*, go to the **Overview** of the selected domain and scroll down to find the *Account ID* as shown below.

![Cloudflare: Account ID](/images/blog/cloudflare-account-id.jpg)

Save these details for later.

---

# Create a Cloudflare API Token

Next, we will need to allow the Proxmox ACME protocol to create required DNS validation TXT DNS records. For this, you will need to create an API token on Cloudflare that Proxmox can use during domain validation.

Right below where you found the *Account ID* from the **Overview tab**, you will find **Get your API Token** link, as shown below.

![Cloudflare: Get API Token](/images/blog/cloudflare-get-api-token.jpg)

Click the **Create Token** button to begin:

![Cloudflare: Create Token](/images/blog/cloudflare-create-api-token.jpg)

Cloudflare provides a lot of temoplates for their API Tokens but in this case the template we're most interested in is **Edit zone DNS**, as shown:

![Cloudflare: Select Template](/images/blog/cloudflare-edit-dns-template.jpg)

On the **User API Tokens** page, click the small icon beside the text **Token name: Edit zone DNS** and change the name to something more suitable (e.g. *Proxmox ACME certificate*)

![Cloudflare: Edit token name](/images/blog/cloudflare-edit-name.jpg)

You will need to enable **[Zone] [DNS] [Edit]** and **[Zone] [Zone] [Read]** permissions for this token, as shown below:

![Cloudflare: New API token details](/images/blog/cloudflare-new-api-token-details.jpg)

Recommended, for improved security restrict this token to only the root domain name of your Proxmox server (e.g. alandoyle.com), see above.

Click **Continue to summary**, then click **Create Token** to complete the API Token generation. Copy and save this token, as you did with the Accound ID previously. We will need this later, see below.

![Cloudflare: New API token](/images/blog/cloudflare-new-api-token.jpg)

NOTE: Copy this API Token as there is no way to view it at a later date.

---

# Setup Proxmox Let's Encrypt Accounts

We need to create **TWO** accounts for LetsEncrypt: *Staging* and *Production*. Staging allows you to test before getting the real certificate. Staging is important because, if there are too many attempts, Let's Encrypt will block you for a certain period of time.

To create the Let's Encrypt accounts, go to **Datacenter**->**ACME** and click **Add** under **Accounts**.

First, we will add the staging account as shown below.

![Proxmox: ACME Account](/images/blog/proxmox-acme-account.jpg)

Provide something recognizable for Account Name (e.g. Staging). Email can be any valid email (it does not have to be on your domain). Then, choose **Let's Encrypt V2 Staging** for **ACME Directory**, check **Accept TOS** and click **Register**.

Repeat the above steps but change the Account Name (e.g. Production). Again add a valid email. Then, choose **Let's Encrypt V2** for **ACME Directory**, check **Accept TOS** and click **Register**.

If successful, the Proxmox task viewer window should popup after clicking **Register** and say **TASK OK**. Simply close this popup to continue.

---

# Add ACME Challenge Plugin for Cloudflare

Next, we are ready to verify that we own the domain, using ACME DNS-01 challenge. So we need to add the **Cloudflare managed DNS** plugin by clicking **Add** under **Challenge Plugins**.

![Proxmox: ACME DNS Plugin](/images/blog/proxmox-acme-dns-plugin.jpg)

We are going to call this **Cloudflare-DNS01** (no spaces allowed). Pick **Cloudflare Managed DNS** for **DNS API**. Finally, copy-paste the *Account ID* and *Cloudflare API Token* we stored previously and click the **Add** button to continue.

---

# Add Domain Name for ACME Challenge

Now, go to the Node/Proxmox Host (not Datacenter), in this case mine is called **sif** and find the **Certificates** tab, see below.

![Proxmox: ACME Certificates](/images/blog/proxmox-acme-certificates.jpg)

Edit the **Using Account** and pick the **Staging** account we created previously.

![Proxmox: ACME Certificates](/images/blog/proxmox-acme-create-domain.jpg)

Next, click Add and add a domain as shown above. For **Challenge Type** pick **DNS** and for **Plugin** choose the one we added in the previous step (*Cloudflare-DNS01*).

Click **Create** to continue.

---

# Order Let's Encrypt SSL Certificate Proxmox

Highlight the domain you created (e.g. proxmox.alandoyle.com) and click **Order Certificates Now**. After few seconds or couple of minutes, the Proxmox task viewer should show that the certificates were download and end with **TASK OK**.

{{< warning "If you try to use the staging certificate you will still receive a security warning." >}}

And finally, if the staging was successful, we can now order the real Proxmox SSL certificate.

Change the Using Account to **Production** we created previously. Then, highlight the domain and click **Order Certificates Now.**, see below.

![Proxmox: Generate Production Certificates](/images/blog/proxmox-acme-create-certificate.jpg)

{{< warning "The Proxmox web interface should reload during this process and enable the new certificate. You will still get a *Security Warning* until you change the URL in your browser to match the certificate (e.g. https://proxmox.alandoyle.com:8006)" >}}

---

# Certificate Renewal

After setting up Proxmox Let's Encrypt certificates, you can basically forget about it. Renewal happens automatically in the background every few days.

# Conclusion

Having valid SSL certificates for Proxmox is nicer, secure, and convenient compared to self-signed certificates which satisfies my [Why?](/blog/proxmox-letsencrypt-ssl-certificates/#why).

{{< warning "If your Proxmox server has been set up using [my simple script](/blog/proxmox-initial-setup/) then you will need to run *setup-https* as *root* to use the new certificate for the custom web interface listening on the main HTTPS port (443)." >}}