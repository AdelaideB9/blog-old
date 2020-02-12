---
layout: post
title: NeverLAN CTF 2020 Write-up
date: 2020-02-08T14:30:10.000+00:00
author: dotjam
categories:
- ctf
- writeups
header_img: https://blog.cyanic.me/assets/img/NeverLAN%202020.jpg

---
## Browser Bias##

This challenge gives us very little information, just a url to a site that tells us `Sorry, this site is only optimized for browsers that run on commodo 64`. However, this also narrows our focus down to a singular goal - trying to convince the website that we are accessing it from a whatever a 'commodo 64' is.

The first thing we need to know is how the  