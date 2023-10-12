---
title: "Switched to GrapheneOS"
faicon: "fa-brands fa-android"
date: 2023-10-12T14:40:50+01:00
publishDate: 2023-10-12T14:40:50+01:00
images: []
draft: false
tags: [grapheneos,android,nokia x20,pixel 6a]
cover:
    image: "/images/blog/grapheneos_logo.png"
    alt: "Image Description"
    relative: false
---

# What is GrapheneOS?

From the [GrapheneOS website](https://grapheneos.org)...

> The private and secure mobile operating system with Android app compatibility.
> Developed as a non-profit open source project.

Basically, distilling it's purpose down into simple terms: it's a secure de-Googled Android operating system.

# Why did I move to GrapheneOS?

Firstly, I'm not paranoid about privacy but on the other hand I don't feel comfortable with large corporations, like Google, Microsoft, etc, knowing everything I do and everywhere I go. GrapheneOS presents a good compromise between privacy and convenience with it's sandboxed Google Play Services. It's the best of both worlds.

Secondly, Google has a nasty habit of cancelling products people use, e.g. Google Reader/Google Domains/Google Stadia/G Suite (Legacy Free Edition)/Google+/Inbox/etc., [the list goes on](https://killedbygoogle.com/). So, naturally, I've started to distrust Google products more and more as I've gotten fed up investing time in them only to see them cancelled. It's data collection is also becoming more invasive.

# Changes I've made prior to GrapheneOS

For convenience and support I'd always used Google Chrome _and_ [Mozilla Firefox](https://www.mozilla.org/en-GB/firefox/products/) but I've recently swapped out Google Chrome for [Brave](https://brave.com/), which has better  privacy options and defaults, and I've begun slowly purging my data from Google services.

In the spirit of self-reliance I now selfhost a _lot_ of services.
Including...
- [Nextcloud](https://nextcloud.com/) - Replaced Google Drive/Dropbox/OneDrive/etc.
- [Navidrome](https://www.navidrome.org/) - Replaced Google Music/Spotify, which I use with the [Ultrasonic](https://f-droid.org/en/packages/org.moire.ultrasonic/) client..
- [AudioBookshelf](https://www.audiobookshelf.org/) - Replaced Audible (I know this one is Amazon owned but still), with [experimental Android app](https://github.com/advplyr/audiobookshelf-app/releases).
- [Jellyfin](https://jellyfin.org/) - Augments Netflix/Disney+/Amazon Prime Videos with my collection of DVD's and Blu-rays. Again technically not Google but same idea. I'm one of those "strange" people who still likes to buy CD's, DVD's and Blu-rays. I guess I'm a hoarder, which probably explains all the old/retro computers I own too...
- [TT-RSS](https://tt-rss.org/) - Replaced Google Reader (shutdown a long time ago)
- [xBrowserSync](https://github.com/xbrowsersync/api-docker/blob/master/README.md) - Replaces Google Chrome bookmark syncing.
- [VaultWarden](https://github.com/dani-garcia/vaultwarden) - Use instead of built-in Password Manager in conjunction with the [Bitwarden](https://bitwarden.com/) Android client.

I also replaced a lot of the stock Android software with more open equivalents.
Including...
- [F-Droid](https://f-droid.org) - ~~Replaces~~ Augments Google Play Store.
- [Quillpad](https://f-droid.org/en/packages/io.github.quillpad/) - Replaces Google Keep. It also integrates nicely with Nextcloud.
- [Aegis Authenticator](https://f-droid.org/en/packages/com.beemdevelopment.aegis/) - Replaces Google Authenticator.
- [NewPipe](https://f-droid.org/en/packages/org.schabi.newpipe/) - Replaces YouTube.
- [FairEmail](https://f-droid.org/en/packages/eu.faircode.email/) - Replaces the GMail app.
- [QKSMS](https://f-droid.org/en/packages/com.moez.QKSMS/) - Replaces "Messages by Google" app.

I still have an old "Google Apps for my Domain" account which they [tried to charge for](https://www.techradar.com/news/bad-news-youll-have-to-start-paying-for-google-workspace-this-year) but that backfired and [they had to backtrack](https://arstechnica.com/gadgets/2022/05/google-backtracks-on-legacy-gsuite-account-shutdown-wont-take-user-emails/). I'm still looking into secure and private alternatives to this, I'm currently leaning towards [ProtonMail](https://proton.me/mail) because it's reasonably priced (less than £50 per year), good support and based in Switzerland so minig my emails (as boring as they  are) is not permitted.

NOTE: I'm a firm believer of "If you're not paying for the Product, then 'You' are the Product!".

# What benefits do I get from GrapheneOS?

For me GrapheneOS is a solid base for a phone operating system.  I did install their [sandboxed Google Play](https://grapheneos.org/usage#sandboxed-google-play) as a balance between privacy and convenience. As a sandboxed app it'll behave like other apps on the phone and not have invasive access like on regular Android. The GrapheneOS team explain this much better on their [website](https://grapheneos.org/usage#sandboxed-google-play).

# What are the downsides of GrapheneOS?

The main "victims" of the de-Googling process is Google Pay and Android Auto. Neither of these are vitally important and I'm quite happy without them.

# Can I use it on my current phone?

Short answer: NO.

Long answer: YES, BUT...

My current phone is/was a Nokia X20, which is a really nice and simple phone. It's supplied with a stock Android One install and is based on Android 13 (as of October 2023). All in all a nice, clean Android phone. I did disable as many unwanted Google services as I could to lighten the load but Google Play Services is tightly integrated within the OS and constantly reports to Google basically everything you do and everywhere you go!

Unfortunately, there are no custom ROM's available for the Nokia X20 (TA-1341) and GrapheneOS is defintely out. In fact, GrapheneOS _ONLY_ installs on [certain Google Pixel devices](https://grapheneos.org/faq#supported-devices). Oh the irony!

So I went on eBay and picked up a pristine, refurbished Google Pixel 6a which now runs GrapheneOS and replaces the Nokia X20 as my daily driver.

I won't describe how to install GrapheneOS here but I did follow the following [Youtube video](https://www.youtube.com/watch?v=qruzL9IdUr8) by **[Side of Burittos](https://www.youtube.com/@sideofburritos)**
# Conclusion

Was it worth switching to GrapheneOS?

- Well for me I'd have to say absolutely and the secure, privacy respecting defaults of GrapheneOS are fantastic for a self-hoster like myself.

Do I miss the features incompatible with GrapheneOS?

- No, not at all. As much as GooglePay is handy I prefer using physical cards it helps me keep mental "count" of all my expenses, and Android Auto is all well and nice but I only ever used it for music which works perfectly over Bluetooth so again no loss.