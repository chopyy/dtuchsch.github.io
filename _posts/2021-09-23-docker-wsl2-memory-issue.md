---
layout: post
title:  "Stopping Docker from Consuming  of Docker for WSL2"
date:   2021-09-23 16:21:00
categories: docker
---

I've experienced that Docker consumes a lot of RAM (more than 4 GB) when using the Docker WSL2 integration even in idle mode.

There's an easy fix available to limit the amount of memory consumed by Docker running under Windows. [Have a look at this medium.com article](https://medium.com/@lewwybogus/how-to-stop-wsl2-from-hogging-all-your-ram-with-docker-d7846b9c5b37).