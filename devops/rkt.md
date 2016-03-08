# Rkt
[Source](http://blog.codeship.com/getting-started-rkt/)

Rkt images are build with the ``acbuild`` command. Put them in a shell script to define the image.
```
acbuild begin
acbuild set-name hello
acbuild copy hello /app/hello
acbuild set-working-directory /app
acbuild set-exec -- /app/hello
acbuild write --overwrite hello-latest-linux-amd64.aci
```

Some basic ``rkt`` commands:
- ``sudo rkt image list``: List available images
- ``sudo rkt list``: List running containers
- ``sudo rkt --insecure-options=image run hello-latest-linux-amd64.aci``: Run an unsigned image
- ``sudo rkt gc``: Clean up unused containers
- ``sudo rkt image gc``: Clean up images

### Systemd
``rkt`` uses ``systemd`` to manage containers. The following ``systemd`` commands are useful:
- ``machinectl list``: List running machines
- ``journalctl -M MACHINEID``: Display logs from container
- ``machinectl kill MACHINEID``: Stop a container

### Working with Docker files
Use [``docker2aci``](https://github.com/appc/docker2aci) to convert *Docker* images to *rkt* images:
- ``docker2aci docker://imageurl``: Convert *Docker* image and squash layers into one *ACI* file.
- ``docker2aci --nosquash docker://imageurl``: Convert *Docker* image into *ACI* file and keep layers separate.
- ``sudo rkt run --insecure-options=image docker://imageurl``: Run a *Docker* image directly with *rkt*.

### Image discovery and distribution
*rkt* uses a combination of *HTTPS* and *HTML* ``meta`` tags to point to images. Images can be stored on [Quay](https://quay.io/).
```html
<meta
  name="ac-discovery"
  content="quay.io https://quay.io/c1/aci/{name}/{version}/{ext}/{os}/{arch}/">
<meta
  name="ac-discovery-pubkeys"
  content="quay.io https://quay.io/aci-signing-key">
```

### Pods
*Pod* is a logical unit of a collection of containers that should be executed together and live in a shared network space.
```
sudo rkt run --volume volume--var-lib-redis,kind=host,source=/var/lib/redis quay.io/quay/redis \\
  --insecure-options=image --port=http:9000 --set-env REDIS_HOST=localhost \\
  ~/Development/demo-api-redis/demo-api-redis-latest-linux-amd64.aci
```

### Limiting resources with CGroups
```
sudo rkt run --cpu=500 --memory=512M quay.io/quay/redis --insecure-options=image \\
  --port=http:9000 --set-env REDIS_HOST=localhost demo-api-redis-latest-linux-amd64.aci
```
