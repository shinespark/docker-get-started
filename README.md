
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
