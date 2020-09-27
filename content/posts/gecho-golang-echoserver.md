---
title: "Gecho Golang Echoserver"
date: 2020-09-27T06:41:07Z
draft: false
---

One of my favorite tiny projects for learning a new language is implementing
an echo server. An echo server is an HTTP listener that responds back to
the client with attributes about the incoming request. It can return things like
the client's IP address, headers sent by the client, and anything else the
server is privy to.

It's such a simple thing, but it can be very useful for testing proxy setups, 
understanding complex request flows, header handling, cache behavior,
etc. I often stand one up when I am troubleshooting things as a first step to
sanity check assumptions about what the server might be seeing from the client.

Tonight, I spent some time implementing an echo server with Golang to learn
about its basic methods and syntax. The result is
[gecho](https://github.com/mshade/gecho), a tiny listener that will respond
by echoing request attributes back at. It has two endpoints -- a default at `/`,
and `/ip` which simply returns the IP of the client.

You can hit it at [reflec.me](https://reflec.me).
A container is available at [mshade/gecho](https://hub.docker.com/r/mshade/gecho),
weighing in at less than 4MB.

Test it out with docker:
```bash
$ docker run --name gecho -d --rm -p 8090:8090 mshade/gecho

$ curl http://localhost:8090/ -H "Hello: world"
Host: localhost:8090
Remote Address: 172.17.0.1:49662
RequestURI: /

Headers
Accept: */*
Hello: world
User-Agent: curl/7.72.0
```

