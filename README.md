[![build status][251]][232] [![commit][255]][231] [![version:x86_64][256]][235] [![size:x86_64][257]][235] [![version:armhf][258]][236] [![size:armhf][259]][236] [![version:armv7l][260]][237] [![size:armv7l][261]][237] [![version:aarch64][262]][238] [![size:aarch64][263]][238]

## [Alpine-Supervisor][234]
#### Container for Alpine Linux + Supervisor Init System
---

This [image][233] serves as the base image for
applications / services that need an init system to launch the processes and
pass the proper signals when interacted with the containers.

Based on [Alpine Linux][131] from my [alpine-base][132] image with
the [Supervisor][133]([Github][134]) init system overlayed on it.

The image is tagged respectively for the following architectures,
* **armhf**
* **armv7l**
* **aarch64**
* **x86_64** (retagged as the `latest` )

**non-x86_64** builds have embedded binfmt_misc support and contain the
[qemu-user-static][105] binary that allows for running it also inside
an x86_64 environment that has it.

---
#### Get the Image
---

Pull the image for your architecture it's already available from
Docker Hub.

```
# make pull
docker pull woahbase/alpine-supervisor:x86_64
```

---
#### Run
---

If you want to run images for other architectures, you will need
to have binfmt support configured for your machine. [**multiarch**][104],
has made it easy for us containing that into a docker container.

```
# make regbinfmt
docker run --rm --privileged multiarch/qemu-user-static:register --reset
```

Without the above, you can still run the image that is made for your
architecture, e.g for an x86_64 machine..

For custom configuration, replace the `/etc/supervisord.conf` with
your own. By default, the supervisor configuration `ini` files
inside `/etc/supervisor.d/` are sourced so you can also put your
application configurations there, or volume mount the directory from
your local.

Running `make` starts the service.

```
# make
docker run --rm -it \
  --name docker_supervisor --hostname supervisor \
  -v conf:/etc/supervisor.d \
  woahbase/alpine-supervisor:x86_64
```

Running `make shell` gets a shell inside the container, but does
not run the init system, so that it can be done manually.

```
# make
docker run --rm -it \
  --entrypoint /bin/bash \
  --name docker_supervisor --hostname supervisor \
  -v conf:/etc/supervisor.d \
  woahbase/alpine-supervisor:x86_64
```

Stop the container with a timeout, (defaults to 2 seconds)

```
# make stop
docker stop -t 2 docker_supervisor
```

Removes the container, (always better to stop it first and `-f`
only when needed most)

```
# make rm
docker rm -f docker_supervisor
```

Restart the container with

```
# make restart
docker restart docker_supervisor
```

---
#### Shell access
---

Get a shell inside a already running container,

```
# make debug
docker exec -it docker_supervisor /bin/bash
```

set user or login as root,

```
# make rdebug
docker exec -u root -it docker_supervisor /bin/bash
```

To check logs of a running container in real time

```
# make logs
docker logs -f docker_supervisor
```

---
### Development
---

If you have the repository access, you can clone and
build the image yourself for your own system, and can push after.

---
#### Setup
---

Before you clone the [repo][231], you must have [Git][101], [GNU make][102],
and [Docker][103] setup on the machine.

```
git clone https://github.com/woahbase/alpine-supervisor
cd alpine-supervisor
```
You can always skip installing **make** but you will have to
type the whole docker commands then instead of using the sweet
make targets.

---
#### Build
---

You need to have binfmt_misc configured in your system to be able
to build images for other architectures.

Otherwise to locally build the image for your system.
[`ARCH` defaults to `x86_64`, need to be explicit when building
for other architectures.]

```
# make ARCH=x86_64 build
# sets up binfmt if not x86_64
docker build --rm --compress --force-rm \
  --no-cache=true --pull \
  -f ./Dockerfile_x86_64 \
  --build-arg DOCKERSRC==woahbase/alpine-base:x86_64 \
  -t woahbase/alpine-supervisor:x86_64 \
  .
```

To check if its working..

```
# make ARCH=x86_64 test
docker run --rm -it \
  --name docker_supervisor --hostname supervisor \
  woahbase/alpine-supervisor:x86_64 \
  ' --version'
```

And finally, if you have push access,

```
# make ARCH=x86_64 push
docker push woahbase/alpine-supervisor:x86_64
```

---
### Maintenance
---

Sources at [Github][106]. Built at [Travis-CI.org][107] (armhf / x64 builds). Images at [Docker hub][108]. Metadata at [Microbadger][109].

Maintained by [WOAHBase][204].

[101]: https://git-scm.com
[102]: https://www.gnu.org/software/make/
[103]: https://www.docker.com
[104]: https://hub.docker.com/r/multiarch/qemu-user-static/
[105]: https://github.com/multiarch/qemu-user-static/releases/
[106]: https://github.com/
[107]: https://travis-ci.org/
[108]: https://hub.docker.com/
[109]: https://microbadger.com/

[131]: https://alpinelinux.org/
[132]: https://hub.docker.com/r/woahbase/alpine-base
[133]: http://supervisord.org/index.html
[134]: https://github.com/Supervisor/supervisor

[201]: https://github.com/woahbase
[202]: https://travis-ci.org/woahbase/
[203]: https://hub.docker.com/u/woahbase
[204]: https://woahbase.online/

[231]: https://github.com/woahbase/alpine-supervisor
[232]: https://travis-ci.org/woahbase/alpine-supervisor
[233]: https://hub.docker.com/r/woahbase/alpine-supervisor
[234]: https://woahbase.online/#/images/alpine-supervisor
[235]: https://microbadger.com/images/woahbase/alpine-supervisor:x86_64
[236]: https://microbadger.com/images/woahbase/alpine-supervisor:armhf
[237]: https://microbadger.com/images/woahbase/alpine-supervisor:armv7l
[238]: https://microbadger.com/images/woahbase/alpine-supervisor:aarch64

[251]: https://travis-ci.org/woahbase/alpine-supervisor.svg?branch=master

[255]: https://images.microbadger.com/badges/commit/woahbase/alpine-supervisor.svg

[256]: https://images.microbadger.com/badges/version/woahbase/alpine-supervisor:x86_64.svg
[257]: https://images.microbadger.com/badges/image/woahbase/alpine-supervisor:x86_64.svg

[258]: https://images.microbadger.com/badges/version/woahbase/alpine-supervisor:armhf.svg
[259]: https://images.microbadger.com/badges/image/woahbase/alpine-supervisor:armhf.svg

[260]: https://images.microbadger.com/badges/version/woahbase/alpine-supervisor:armv7l.svg
[261]: https://images.microbadger.com/badges/image/woahbase/alpine-supervisor:armv7l.svg

[262]: https://images.microbadger.com/badges/version/woahbase/alpine-supervisor:aarch64.svg
[263]: https://images.microbadger.com/badges/image/woahbase/alpine-supervisor:aarch64.svg
