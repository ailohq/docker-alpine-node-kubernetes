# docker-alpine-node-kubernetes
Minimal Node.js Docker Images built on Alpine Linux with support for DNS service discovery

---------------------------------------------------------

A port of [janeczku/alpine-kubernetes](https://hub.docker.com/r/janeczku/alpine-kubernetes/) to use [mhart/alpine-node](https://hub.docker.com/r/mhart/alpine-node/) as its base image
to provide DNS support for  micro-services containers in Kubernetes, Console,
Docker Cloud or other Docker cluster environments that use DNS-based service discovery
and rely on the containers being able to use the search domains from resolv.conf
for resolving service names.

[![Docker Repository on Quay](https://quay.io/repository/trunk/alpine-node-kubernetes/status "Docker Repository on Quay")](https://quay.io/repository/trunk/alpine-node-kubernetes)

Versions v5.1.0, v4.2.2, v0.12.7 – built on [Alpine Linux](https://alpinelinux.org/).

Example
-------

    $ docker run quay.io/trunk/alpine-node-kubernetes node --version
    v5.1.0

    $ docker run quay.io/trunk/alpine-node-kubernetes:4 node --version
    v4.2.2

    $ docker run quay.io/trunk/alpine-node-kubernetes npm --version
    3.4.0

    $ docker run quay.io/trunk/alpine-node-kubernetes:base node --version
    v5.1.0

Example Dockerfile for your own Node.js project
-----------------------------------------------

If you don't have any native dependencies, ie only depend on pure-JS npm
modules, then my suggestion is to run `npm install` locally *before* running
`docker build` (and make sure `node_modules` isn't in your `.dockerignore`) –
then you don't need an `npm install` step in your Dockerfile and you don't need
`npm` installed in your Docker image – so you can use one of the smaller
`base*` images.

It is recommended you setup you process to run using [s6](http://skarnet.org/software/s6/)
as s6 is using `ENTRYPOINT` to run its `init`. Example usage can be found in
[janeczku/alpine-kubernetes](https://hub.docker.com/r/janeczku/alpine-kubernetes/).

    FROM quay.io/trunk/alpine-node-kubernetes:base
    # FROM quay.io/trunk/alpine-node-kubernetes:base-0.10
    # FROM quay.io/trunk/alpine-node-kubernetes

    WORKDIR /src

    # Copy the s6 service.d file(s)
    # S6 requires and executable (755) runfile to be deployed to:
    # /etc/services.d/{APPLICATION NAME}/run
    COPY rootfs /

    # Copy the application
    COPY bin ./bin

    # If you have native dependencies, you'll need extra tools
    # RUN apk add --update make gcc g++ python

    # If you need npm, don't use a base tag
    # RUN npm install

    # If you had native dependencies you can now remove build tools
    # RUN apk del make gcc g++ python && \
    #   rm -rf /tmp/* /var/cache/apk/* /root/.npm /root/.node-gyp

    EXPOSE 3000
    # If you don't want to use s6, you must use CMD
    # CMD ["node", ".bin/index.js"]

Building
--------

```
./upgrade 6.1.0 4.4.4
git push --tags -f
```

Run `./upgrade [version...]` to produce new `Dockerfile`s tagged with the given versions
with and without `npm`.
This will also manage upgrading partial versions. When the given version is the newest
for the partial it will replace the previous version, including `latest`.

Pushing to master will result in all new tags being built in [quay.io](https://qay.io).


Caveats
-------

As Alpine Linux uses musl, you may run into some issues with environments
expecting glibc-like behaviour (for example, Kubernetes). Some of these issues
are documented here:

- http://gliderlabs.viewdocs.io/docker-alpine/caveats/
- https://github.com/gliderlabs/docker-alpine/issues/8

Inspired by:

- https://github.com/mhart/alpine-node
- https://github.com/janeczku/docker-alpine-kubernetes
- https://github.com/alpinelinux/aports/blob/454db196/main/nodejs/APKBUILD
- https://github.com/alpinelinux/aports/blob/454db196/main/libuv/APKBUILD
- https://hub.docker.com/r/ficusio/nodejs-base/~/dockerfile/
