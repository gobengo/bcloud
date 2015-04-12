# bcloud

Config files for the bcloud.
Some parts require [gcloud](https://cloud.google.com).

BIG NOTE: This is intentionally experimental so I have some excuse to follow Docker, Kubernetes, etc. If you see me doing something wrong, or wonder about how this whole thing works, file an issue so I can learn or teach or both.

## Features

### docker-compose

Sometimes you need to run your cloud locally.
It can be hard to run a complex set of services in a standard way across e.g. OS X, Linux, and Windows.

Regardless of Host OS, If you can run (a linux vm +) docker, you can run bcloud by doing the following:

1. Install [Docker](https://www.docker.com/) (if you're a Mac user, I highly recommend [boot2docker](http://boot2docker.io/)) and [Docker Compose](https://docs.docker.com/compose/).
2. `docker-compose up`. use `-d` to keep everything detached, if you don't care to watch the logs in stdout)
3. ``open http://`boot2docker ip`/`` (or IP of your DOCKER_HOST, if not using boot2docker) to see a running bengo-web

Wow, that was easy.

See [docker-compose.yml](./docker-compose.yml) for the service definitions.

### Kubernetes Cluster

Running a cloud locally is nice, but eventually you want to deploy to some remote hardware.

To help, this repo includes [Kubernetes](https://github.com/googlecloudplatform/kubernetes)-compatible JSON descriptions of how to use a fleet of VMs to run the bcloud apps with replication, auto-restart, etc.

* [./pods](./pods) - 
  * bengo-web - HTTP handler for [bengo.is](http://bengo.is). Built from Docker image pulled from [gcr.io/bengo-is/bengo-web](gcr.io/bengo-is/bengo-web)
* [./services](./services)
  * bengo-web - A load balancer to route the publicIp:80 (`dig bengo.is`) across all the bengo-web pods.
* [./controllers](./controllers)
  * bengo-web-controller - Says 'always be running 3 pods of bengo-web'. If one dies, it'll be replaced within a second or two.

Why?

* [kylewm.com in indiewebcamp irc](http://indiewebcamp.com/irc/2015-04-11/line/1428774226952): "I want it to be more like php where you can just add new little services everywhere without having to spin up a dedicated application server for each one"

### Public Storage

* `./bin/deploy-public` will rsync ./public with gs://bengo/ ([public link](https://storage.googleapis.com/bengo/index.html))
  * TODO: I could DNS to this as e.g. `storage.bengo.is`

## How do I use that...?

I'm still figuring that out. :)

The main thing I care about is a command to: 're-pull the published bengo-web gcr docker image, and recreate all the pods in the bengo-web-controller'.

Instead, I publish the container
```
gcloud preview docker push gcr.io/bengo-is/bengo-web
```

Then... I don't remember.
* I think I just killed all the bengo-web pods. Then they rebooted and had latest image.
* That sounds too simple...
* #TODO - Update the next time I deploy [bengo.is src code](https://github.com/gobengo/bengo.is)

### Adding a new system to the cloud

1. Go write your app (or use someone elses)
2. Publish a Docker image of it somewhere (like GCR or docker hub)
3. Add to ./controllers describing how many replicas you want, and a pod template
4. If this service should be publicly available on the network (and not just some e.g. job-processing pod)
  * Create a ./service for it that describes the port mappings
  * Via a means outside of Kubernetes (like Google Console or other gcloud commands), create a path from the public network to the k8s nodes
    * TODO Elaborate/Document next time

