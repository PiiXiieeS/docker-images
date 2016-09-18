
### Creating a minimal docker image for the Raspberry PI 3

We will start with a minimal image based on the Linux distribution Alpine for
ARM

```
$ docker pull easypi/alpine-arm  
```

This distribution is very small and you can check that with **docker images**
command. You can see the comparison with other distributions like rpi-node
based on raspbian or ghost-on-docker based on ubuntu.

The new image easypi/alpine-arm is less than 6MB !


```
REPOSITORY                   TAG                 IMAGE ID            CREATED SIZE
hypriot/rpi-node             latest              16bae966691d        2 weeks ago         475.8 MB
easypi/alpine-arm            latest              4a9aaa4b4d58        11 weeks ago        5.999 MB
alexellis2/ghost-on-docker   armv7               ba9e25b6aa98        4 months ago        884.7 MB
```

We can now run a container with that new image and the command line to install
nodejs and get some meat.

```
$ docker run -it easypi/alpine-arm sh
```

From the command line we can use the following commends to inquire the version of the OS and distribution:

```
/ # uname -r
4.4.13-v7+
/ # uname -mrs
Linux 4.4.13-v7+ armv7l
/ # uname -a
Linux cf230398be61 4.4.13-v7+ #894 SMP Mon Jun 13 13:13:27 BST 2016 armv7l Linux
```

Now we can use the package manager available in Alpine apk to install node and
npm. `apk add nodejs` will download and install directly the last version :


```  
/ # apk add nodejs
WARNING: Ignoring APKINDEX.c919c16d.tar.gz: No such file or directory
(1/4) Installing libgcc (5.3.0-r0)
(2/4) Installing libstdc++ (5.3.0-r0)
(3/4) Installing libuv (1.9.1-r0)
(4/4) Installing nodejs (6.2.0-r0)
Executing busybox-1.24.2-r9.trigger
OK: 40 MiB in 20 packages
/ #  
/ # node -v
v6.2.0
/ # npm -v
3.8.9
```

Now we will exit the container and will use it to generate another image so in the future we can run new containers directly with nodejs installed right
away.

Notice we did not used the option `--rm` that would have deleted the
container with all the changes done.

Command `commit` with options `-m` to specify a description of the change
and option `-a` to specify the author. We need to identify the container ID
(36136c1e0936) and the name of the new image in the format
`organization/repository:tag`.

```
$ docker commit -m "Added NodeJS to Alpine ARM Linux distribution" -a "Jose Gascon" 36136c1e0936 piixiiees/node-alpine-arm:v6  
```

Now at any time we can run a new container based on the new image running
already nodejs and npm.

```
$ docker run \--name node-alpine -it piixiiees/node-alpine-arm:v6 sh
```

Of course this new image is a bit larger than the original one but yet very
small, just 32MB.

```
REPOSITORY                   TAG                 IMAGE ID            CREATED SIZE
piixiiees/node-alpine-arm    v6                  a4ff5ca388d3        2 hours ago         32.13 MB
hypriot/rpi-node             latest              16bae966691d        2 weeks ago         475.8 MB
easypi/alpine-arm            latest              4a9aaa4b4d58        11 weeks ago        5.999 MB
alexellis2/ghost-on-docker   armv7               ba9e25b6aa98        4 months ago        884.7 MB
```

This link contains a dokerfile and a compilation script to build a similar
image out of `resin/armhf-alpine:latest` distribution.
<https://github.com/resin-io/node-arm/tree/master/alpine/armhf>

### Creating a minimal docker image from a Dokerfile for the Raspberry PI 3

Now we will create a dockerfile that will automatize the creation of the base
image and the installation of nodejs and npm.

```
**FROM** easypi/alpine-arm
**MAINTAINER** Jose Gascon &lt;piixiiees@gmail.com&gt;
**RUN** apk add nodejs
```

From the same directory of the dockerfile we can execute the command to build
a new image.

```
$ docker build -t piixiiees/arm-alpine-nodev6:v6 .
```

The new image is listed and with a size of 32MB.

```
REPOSITORY                    TAG                 IMAGE ID            CREATED SIZE
piixiiees/arm-alpine-nodev6   v6                  504ca49a491c        9 minutes ago       32.12 MB
piixiiees/node-alpine-arm     v6                  a4ff5ca388d3        4 hours ago         32.13 MB
easypi/alpine-arm             latest              4a9aaa4b4d58        11 weeks ago        5.999 MB
alexellis2/ghost-on-docker    armv7               ba9e25b6aa98        4 months ago        884.7 MB
```

Now we can create new containers with the nodejs installed in a base alpine
image.

```
$ docker run --name node-alpine -it piixiiees/arm-alpine-nodev6:v6 sh
```

More information about how to build your own images at
<https://docs.docker.com/engine/tutorials/dockerimages/>
