---
title: Faster Builds with Docker Caching
author: Nancy Chauhan
date: 2020-05-20
hero: ./images/docker.png
excerpt: Faster Builds with Docker caching

---

Recently was working around speeding up CI Build, which took time around `50 min`. This post is about speeding up builds with Docker caching and Buildkit. 
It is a little concept, but it is significant to know as this helped me to reduce 
timings from `50 min` to `15 min`.

### Docker Caching

Docker caches each layer as an image is built, and each layer will only be re-built if it or the layer above it has changed since the last build. 
So, you can significantly speed up builds with Docker cache. 

A better way would be to have multiple COPY instructions that install the packages and dependencies first and then copies your code.

To avoid invalidating the cache:

1. Start your Dockerfile with commands that are less likely to change
2. Place commands that are more likely to change (like COPY . .) as late as possible
3. Add only the necessary files (use a .dockerignore file)

Example : 

The following Dockerfile is for a simple node js project which works great 
but here cache is not used : 

```
FROM mhart/alpine-node

WORKDIR /src
# Copy your code in the docker image
COPY . /src
# Install your project dependencies
RUN npm install
# Expose the port 3000
EXPOSE 3000
# Set the default command to run when a container starts
CMD ["npm", "start"]
```

Optimized Dockerfile :

Here `step 7` is the only step where an image is rebuilt based on 
previous steps when we make a change to our source code.

```
FROM mhart/alpine-node:5.6.0
WORKDIR /src
# Expose the port 3000
EXPOSE 3000
# Set the default command to run when a container starts
CMD ["npm", "start"]
# Install app dependencies
COPY package.json /src
RUN npm install
# Copy your code in the docker image
COPY . /src
```

### BuildKit

If you're using a Docker version >= 19.03 you can use BuildKit, a container image builder. 
Without BuildKit, if an image doesn't exist on your local image registry, you would need to pull the remote images before building in order to take advantage of Docker layer caching.

Example:

```
$ docker pull mjhea0/docker-ci-cache:latest

$ docker build --tag mjhea0/docker-ci-cache:latest .
```
With BuildKit, you don't need to pull the remote images before building since it caches each build layer in your image registry. Then, when you build the image, each layer is downloaded as needed during the build.

To enable BuildKit, set the DOCKER_BUILDKIT environment variable to 1. Then, to turn on the inline layer caching, use the BUILDKIT_INLINE_CACHE build argument.

Example:

```
export DOCKER_BUILDKIT=1

# Build and cache image
$ docker build --tag mjhea0/docker-ci-cache:latest --build-arg BUILDKIT_INLINE_CACHE=1 .

# Build an image from remote cache
$ docker build --cache-from mjhea0/docker-ci-cache:latest .
```
Originally Posted here : https://todayilearnt.xyz/posts/nancy/faster_builds_with_docker_caching/
