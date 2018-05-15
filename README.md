
[Get Started | Docker Documentation](https://docs.docker.com/get-started/)

## Part 2: Containers

### Build the app

```
$ docker build -t friendlyhello .
```

### Run the app

```
$ docker run -p 4000:80 friendlyhello
```

detached mode.

```
# run
$ docker run -d -p 4000:80 friendlyhello

# stop
$ docker container stop <CONTAINER ID>
```

### Tag the image

```
$ docker tag friendlyhello john/get-started:part2
```

### Publishe the image

```
$ docker push john/get-started:part2
```

### Pull and run the image from the remote repository

```
$ docker run -p 4000:80 username/repository:tag
```


## Part 3: Services

### Run your new load-balanced app

```
# init
$ docker swarm init

# stack deploy
$ docker stack deploy -c docker-compose.yml getstartedlab

# service ls
$ docker service ls

# service ps
$ docker service ps getstartedlab_web
```


### Scale the app

Change the `replicas` value in `docker-compose.yml`, and re-runnnig the `docker stack deploy` command~:

```
$ docker stack deploy -c docker-compose.yml getstartedlab
```

### Take down the app and the swarm

```
# take the app down
$ docker stack rm getstartedlab

# take down the swarm
$ docker swarm leave --force
```


## Part 4: Swarms

### Create a cluster

Create a couple of VMs using `docker-machine`, using the VirtualBox driver:

```
$ docker-machine create --driver virtualbox myvm1
$ docker-machine create --driver virtualbox myvm2
```

List the VMs:

```
$ docker-machine ls
NAME    ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
myvm1   -        virtualbox   Running   tcp://192.168.99.100:2376           v18.05.0-ce
myvm2   -        virtualbox   Running   tcp://192.168.99.101:2376           v18.05.0-ce
```

Initialize the swarm and add nodes:

```
$ docker-machine ssh myvm1 "docker swarm init --advertise-addr <myvm1 ip>"
$ docker-machine ssh myvm2 "docker swarm join --token <token> <myvm1 ip>:2377"
```

```
$ docker-machine ssh myvm1 "docker node ls"
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
l423qfwln7tz6zujwqggbpffa *   myvm1               Ready               Active              Leader              18.05.0-ce
piixiz5ng2z1e8ipxx9pwmgnw     myvm2               Ready               Active                                  18.05.0-ce
```


### Deploy your app on the swarm cluster

```
$ docker-machine env myvm1
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.100:2376"
export DOCKER_CERT_PATH="/Users/yourname/.docker/machine/machines/myvm1"
export DOCKER_MACHINE_NAME="myvm1"
# Run this command to configure your shell:
# eval $(docker-machine env myvm1)
```

Run this command to configure your shell:

```
$ docker-machine ls
NAME    ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
myvm1   -        virtualbox   Running   tcp://192.168.99.100:2376           v18.05.0-ce
myvm2   -        virtualbox   Running   tcp://192.168.99.101:2376           v18.05.0-ce

$ eval $(docker-machine env myvm1)
```

Now activate machine.

```
$ docker-machine ls
NAME    ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
myvm1   *        virtualbox   Running   tcp://192.168.99.100:2376           v18.05.0-ce
myvm2   -        virtualbox   Running   tcp://192.168.99.101:2376           v18.05.0-ce
```


### Deploy the app on the swarm manager

```
$ docker stack deploy -c docker-compose.yml getstartedlab
```

```
$ docker stack ps getstartedlab
ID                  NAME                  IMAGE                            NODE                DESIRED STATE       CURRENT STATE             ERROR               PORTS
pg435t4ilkwc        getstartedlab_web.1   shinespark64/get-started:part2   myvm1               Running             Preparing 8 seconds ago
701b4m59qnss        getstartedlab_web.2   shinespark64/get-started:part2   myvm2               Running             Preparing 8 seconds ago
g4yzi7cp73yz        getstartedlab_web.3   shinespark64/get-started:part2   myvm1               Running             Preparing 8 seconds ago
lr9r8ydehipm        getstartedlab_web.4   shinespark64/get-started:part2   myvm2               Running             Preparing 8 seconds ago
af5n54s7xm0m        getstartedlab_web.5   shinespark64/get-started:part2   myvm2               Running             Preparing 8 seconds ago
```

### Accessing your cluster

You can access your app from the IP address of either `myvm1` or `myvm2`.

### Cleanup and reboot

Stacks and swarms.

```
$ docker stack rm getstartedlab
```

Unsetting docker-machine shell variable settings.

```
$ eval $(docker-machine env -u)
```

Restarting Docker machines

```
$ docker-machine start <machine-name>
```

Remove Docker machines

```
$ docker-machine rm <machine-name>
```
