# docker-alpine-node-kubernetes
Minimal Node.js Docker Images built on Alpine Linux with support for DNS service discovery

---------------------------------------------------------

A port of [janeczku/alpine-kubernetes](https://hub.docker.com/r/janeczku/alpine-kubernetes/) to use [mhart/alpine-node](https://hub.docker.com/r/mhart/alpine-node/) as its base image
to provide DNS support for  micro-services containers in Kubernetes, Console,
Docker Cloud or other Docker cluster environments that use DNS-based service discovery
and rely on the containers being able to use the search domains from resolv.conf
for resolving service names. Versions built on [Alpine Linux](https://alpinelinux.org/).

[![Docker Repository on Quay](https://quay.io/repository/trunk/alpine-node-kubernetes/status "Docker Repository on Quay")](https://quay.io/repository/trunk/alpine-node-kubernetes)


Tags
----
* Full install built with npm:
  * `8.13.0` `8.13` `8` `latest`
  * `8.9.4` `8.9` `8`
  * `8.9.0` `8.9` `8`
  * `6.2.1` `6.2` `6`
  * `6.1.0` `6.1` 
  * `6.0.0` `6.0`
  * `5.12.0` `5.12` `5`
  * `5.11.1` `5.11` 
  * `5.11.0`
  * `5.10.1` `5.10` `5`
  * `5.10.0`
  * `4.4.7` `4.4` `4`
  * `4.4.4` 
  * `4.4.3`
  * `4.4.2`
  * `4.4.1`
  * `4.4.0`
  * `4.3.2` `4.3`
  * `4.3.1`
  * `4.3.0`
  * `0.12.15` `0.12` `0`
  * `0.12.14` 
  * `0.12.13`
  * `0.12.12`
  * `0.12.11`
* Base install built as static binary without npm:
  * `base-8.13.0` `base-8.13` `base-8` `base-latest`
  * `base-8.9.4` `base-8.9` `base-8`
  * `base-8.9.0` `base-8.9` `base-8`
  * `base-6.1.0` `base-6.1` `base-6`
  * `base-6.0.0` `base-6.0`
  * `base-5.11.1` `base-5.11` `base-5`
  * `base-5.11.0`
  * `base-5.10.1` `base-5.10` `base-5`
  * `base-5.10.0`
  * `base-4.4.4` `base-4.4` `base-4`
  * `base-4.4.3`
  * `base-4.4.2`
  * `base-4.4.1`
  * `base-4.4.0`
  * `base-4.3.2` `base-4.3`
  * `base-4.3.1`
  * `base-4.3.0`
  * `base-0.12.14` `base-0.12` `base-0`
  * `base-0.12.13`
  * `base-0.12.12`
  * `base-0.12.11`


Example
-------
    $ docker run quay.io/trunk/alpine-node-kubernetes node --version
    v8.9.4

    $ docker run quay.io/trunk/alpine-node-kubernetes node --version
    v6.1.0

    $ docker run quay.io/trunk/alpine-node-kubernetes:5 node --version
    v5.1.0

    $ docker run quay.io/trunk/alpine-node-kubernetes:4 node --version
    v4.4.4

    $ docker run quay.io/trunk/alpine-node-kubernetes npm --version
    v3.4.0

    $ docker run quay.io/trunk/alpine-node-kubernetes:base node --version
    v6.1.0

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
