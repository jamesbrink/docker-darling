# Docker darling

[![CircleCI](https://circleci.com/gh/utensils/docker-darling.svg?style=svg)](https://circleci.com/gh/utensils/docker-darling) [![Docker Pulls](https://img.shields.io/docker/pulls/utensils/darling.svg)](https://hub.docker.com/r/utensils/darling/) [![Docker Stars](https://img.shields.io/docker/stars/utensils/darling.svg)](https://hub.docker.com/r/utensils/darling/) [![](https://images.microbadger.com/badges/image/utensils/darling.svg)](https://microbadger.com/images/utensils/darling "Get your own image badge on microbadger.com") [![](https://images.microbadger.com/badges/version/utensils/darling.svg)](https://microbadger.com/images/utensils/darling "Get your own version badge on microbadger.com")  

![Darling Logo](https://upload.wikimedia.org/wikipedia/commons/thumb/a/ae/Darling_project_logo.png/150px-Darling_project_logo.png)

## About

This is a containerized version of Darling (macOS translation layer). This is an experimental project with the goal to eventually cross compile both iOS and macOS projects in a docker container. I have had some limited success with macOS application builds.

Darling actually uses it's own container system, so running with Docker is a bit redundant, but I would be interested to see it work more gracefully with docker in the future.  

**Pull Requests are always welcome!**

## Building

This image is fairly heavy to build and can take a few hours depending on your system. The resulting image is about **1.5GB** uncompressed.
The build is driven by a `Makefile` so simply run the following:  

```shell
make
```

If you look at the Makefile you will see a variable for `DARLING_GIT_REF` which is used to build the image against a known working git ref since there seems to be no versioning or tagging going on with Darling. This variable is nothing more than a build arg passed to docker so you can build the most recent commit:  

```shell
DARLING_GIT_REF=master make
```
or you can build from a specific commit:
```shell
DARLING_GIT_REF=a00051b580c45b002690422819e9e2ce486f257e make
```

## Usage

Ensure you have kernel sources installed on your host, this is needed to build the darling 
kernel module against the running system on container startup. We run the container in **privileged** mode and inject the module into the host`s kernel. 

We use a volume mount of your host systems kernel sources (read only) so the kernel module can be built on container startup, this is just an attempt to keep the image somewhat portable.

For Manjaro/Arch Linux Hosts run:  

```shell
docker run -i -t \
    -v /lib/modules/"$(uname -r)"/build:/lib/modules/"$(uname -r)"/build:ro \
    --privileged utensils/darling darling shell
```

For Ubuntu/Debian Linux Hosts run:

```shell
docker run -i -t \
    -v /usr/src:/usr/src:ro \
    --privileged utensils/darling darling shell
```

For CentOS/RHEL Linux Hosts run:

```shell
docker run -i -t \
    -v /lib/modules/"$(uname -r)"/build:/lib/modules/"$(uname -r)"/build:ro \
    -v /lib/modules/"$(uname -r)"/modules.builtin:/lib/modules/"$(uname -r)"/modules.builtin:ro \
    -v /lib/modules/"$(uname -r)"/modules.order:/lib/modules/"$(uname -r)"/modules.order:ro \
    --privileged utensils/darling darling shell
```

## License

This docker build project is licensed [MIT](LICENSE).  
Darling is GNU v3, other included assets all have their own licensing.
