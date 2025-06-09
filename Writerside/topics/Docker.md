# Docker

## Docker Engine

Docker Engine is the runtime that builds and runs Docker containers. It is composed of several key stages:

### Build

Developers create containerized applications by packaging code, dependencies, and environment configurations into
read-only **Docker images**.

```bash
docker image pull ubuntu:latest
````

### Ship

Docker images are stored and shared using Docker registries such as **Docker Hub** (default public registry).

```bash
docker search alpine --filter "is-official=true"
```

### Run

Docker **containers** are runtime instances of images.

```bash
docker container run -it --restart always -p 80:8080 ubuntu:latest /bin/bash
docker container run -d --name c1 -p 80:8080 web:latest
```

#### Common `docker run` options

| Option   | Purpose                     |
|----------|-----------------------------|
| `-t`     | Allocate a pseudo-TTY       |
| `-d`     | Run container in background |
| `--name` | Name the container          |
| `--rm`   | Remove container after exit |
| `-p`     | Map container port to host  |
| `-e`     | Set environment variables   |
| `-m`     | Set memory limit            |

```bash
docker run --name hello -d --rm hello-java
```

> A container must have a main process to stay alive.

```bash
docker container exec -it vigilant_borg bash
Ctrl+P+Q    # Detach from container shell
docker container stop vigilant_borg
docker container rm vigilant_borg
docker container rm $(docker container ls -aq) -f
```

## Dockerfile

A Dockerfile defines instructions to build an image.

```text
FROM alpine
LABEL maintainer="example@example.com"
COPY . /src
WORKDIR /src
RUN apk add --update nodejs npm
RUN npm install
EXPOSE 8080
ENTRYPOINT ["node", "app.js"]
```

| Command   | Purpose                                               |
|-----------|-------------------------------------------------------|
| `FROM`    | Base image                                            |
| `COPY`    | Copy files into image                                 |
| `ADD`     | Like COPY, but supports remote URLs and tar unpacking |
| `ENV`     | Set environment variable                              |
| `RUN`     | Execute command during build                          |
| `CMD`     | Default command when running container                |
| `EXPOSE`  | Document listening ports                              |
| `VOLUME`  | Define mount point                                    |
| `WORKDIR` | Set working directory                                 |
| `LABEL`   | Add metadata                                          |

> Use `.dockerignore` to exclude files from build context.

## Images, Volumes, and Networks

```bash
docker image rm <id>           # Remove image
docker image prune -a          # Remove all unused images
docker volume prune            # Remove unused volumes
docker network ls              # List networks
docker volume ls               # List volumes
```

### Build and Push Image

```bash
docker image build -t username/app:latest .
docker tag <image-id> username/app:latest
docker login
docker push username/app:latest
```

## Docker Compose

Tool for defining and running multi-container applications.

`docker-compose.yml` example:

```yaml
version: "3.8"
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - type: volume
        source: counter-vol
        target: /code
    networks:
      - counter-net

  redis:
    image: redis:alpine
    networks:
      - counter-net

networks:
  counter-net:

volumes:
  counter-vol:
```

```bash
docker-compose up -d
docker-compose down
docker-compose ps
docker-compose stop
docker-compose restart
docker-compose rm
```

> Restart policies: `always`, `unless-stopped`, `on-failure`

## Kubernetes Overview

Kubernetes manages containerized applications across clusters of machines.

### Concepts

* **Pod**: Smallest deployable unit; holds one or more containers.
* **Node**: A worker machine (VM or physical).
* **Cluster**: Set of nodes managed by master nodes.
* **Service**: Abstraction for exposing pods.
* **ReplicationController**: Ensures a specific number of pod replicas are running.
* **Label**: Key-value metadata for selection and grouping.
* **Kubelet**: Agent that runs on each node.

### Pod Definition

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    name: couchbase-pod
spec:
  containers:
    - name: couchbase
      image: couchbase
      ports:
        - containerPort: 8091
```

### ReplicationController

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: couchbase-controller
spec:
  replicas: 2
  selector:
    app: couchbase-rc-pod
  template:
    metadata:
      labels:
        app: couchbase-rc-pod
    spec:
      containers:
        - name: couchbase
          image: couchbase
          ports:
            - containerPort: 8091
```

### Service Definition

```yaml
apiVersion: v1
kind: Service
metadata:
  name: couchbase-service
  labels:
    app: couchbase-service-pod
spec:
  ports:
    - port: 8091
  selector:
    app: couchbase-rc-pod
```

## MERN Stack with Docker

Example to run MongoDB:

```bash
docker run -d -p 27017-27019:27017-27019 --name mongodb-service mongo:4.0.4
```

Project structure:

* `server`: Node.js (Express + Socket.IO)
* `client`: React app
* `worker`: Libraries or background jobs

## Resources

* [Play with Docker](https://labs.play-with-docker.com/)
* [Dockerfile Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
* [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)
* [Alpine Docker Image](https://hub.docker.com/_/alpine/)
