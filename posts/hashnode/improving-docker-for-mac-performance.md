---
title: "Improving Docker for Mac performance"
slug: improving-docker-for-mac-performance
publishedAt: 2020-02-04
brief: "Docker makes developing applications a lot easier, but there are a few issues. One of these issues is volume performance in Docker for Mac. One use of volumes is to allow you to keep a folder on your "
tags: ["docker", "devops", "tips", "docker-compose"]
---

Docker makes developing applications a lot easier, but there are a few issues. One of these issues is volume performance in Docker for Mac.

One use of volumes is to allow you to keep a folder on your Mac synced in real-time with a Docker container. This means that you can make changes to your files on your mac and see the changes reflected in Docker straight away; great for development.

But these volumes can be very slow because of how Docker works internally. The issue doesn’t affect Docker on Linux.

Whilst there are no perfect solutions to this, there are options.

When creating a volume, you can specify parameters, one of which is ‘:delegated’. If you only to want to ensure that your development files are synced to the Docker container and you have nothing special going on, this may be suitable for you and will probably improve performance.

The flag is used in docker-compose as follows:

```
services:
  service-name:
    build: service-folder
    volumes:
      - ./service-folder:/container-folder:delegated
```

You can read more about the ‘delegated’ option and other options [here](https://docs.docker.com/docker-for-mac/osxfs-caching/).