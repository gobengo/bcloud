# bcloud

Config files for the bcloud.
Some parts require [gcloud](https://cloud.google.com).

## Features

* `./bin/deploy-public` will rsync ./public with gs://bengo/ ([public link](https://storage.googleapis.com/bengo/index.html))
** TODO: I could DNS to this as e.g. `storage.bengo.is`
* Kubernetes-compatible JSON descriptions of how to use a fleet of VMs
** ./pods - 
*** bengo-web - HTTP handler for [bengo.is](http://bengo.is). Built from Docker image pulled from [gcr.io/bengo-is/bengo-web](gcr.io/bengo-is/bengo-web)
** ./services
*** bengo-web - A load balancer to route the publicIp:80 (`dig bengo.is`) across all the bengo-web pods.
** ./controllers
*** bengo-web-controller - Says 'always be running 3 pods of bengo-web'. If one dies, it'll be replaced within a second or two.

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
