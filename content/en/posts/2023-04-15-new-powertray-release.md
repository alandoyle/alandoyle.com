---
title: "New PowerTray Release"
date: 2023-04-15T23:23:31+01:00
publishDate: 2023-04-15T23:23:31+01:00
images: []
faicon: "fa-brands fa-windows"
draft: false
tags: [powertray,.Net,Windows]
summary: A simple systemtray application for WIndows 10/11 to enhance Power profile usage.
cover:
    image: "/images/covers/panos-sakalakis-35NiG1dYjtM-unsplash_cropped.jpg"
    alt: "featured image"
    relative: false
---

# PowerTray v1.1.3 Released

PowerTray is a simple SysTray application which makes switching Power Plans in Windows 10 (1909 or higher)/Windows 11 easier. As an added bonus it re-enables all the standard Power Plans, as well as the "Ultimate Performance" Power Plan.

![PowerTray v1.1.3](/images/blog/PowerTray-About.jpg)

## History AKA Why?

As with most applications of this type it was written to solve a particular annoyance of Windows, particularily on my laptops and those of my daughters, i.e. simple Power Plan switching.
When gaming on our laptops, while plugged in, we'd set the Power Plan to "Ultimate Performance" for obvious reasons (MWAR POWER!!!!), however when we unplugged the Power Plan remained at "Ultimate Performance" and the batteries drained rapidly :crying_face:

PowerTray initially allowed simple, manual switching because trying to switch Power Plans the "Windows Way":tm: is awful! With v1.1.3 I've added automatic switching of Power Plans between Battery and AC to simplify this process and hopefully prevent excess battery drain. :crossed_fingers:

I also added in repairing all the standard Power Plans because recently I had to reinstall Windows 11 on my main laptop [NYX](/hardware/#asus-fx505dt-al086t) and noticed that I only had a "Balanced" Power Plan! Not much use as I like to set "Ultimate Perfomance" when plugged in and "Power saver" when on the go.

v1.1.3 can restore the following missing Power Plans:
+ Balanced
+ High performance
+ Power saver
+ Ultimate Performance

## New features

+ Repair all missing Power Plans.
+ Add "Ultimate Performance" Power Plan.
+ Better Light/Dark Mode detection.
+ Set Power Plan on startup.
+ Seperate Battery/AC Power Plan selections.
+ Power Plan switching on Power changes i.e. when a laptop is plugged/unplugged from AC power.

![PowerTray SysTray Menu](/images/blog/PowerTray-Menu.jpg)

## Download

All Source Code and Releases are found on [Github](https://github.com/alandoyle/PowerTray/releases/tag/1.1.3).

+ [v1.1.3 Installer](https://github.com/alandoyle/PowerTray/releases/download/1.1.3/PowerTray_Setup.exe)
+ [VS2022 C# Source Code](https://github.com/alandoyle/PowerTray/archive/refs/tags/1.1.3.zip)
