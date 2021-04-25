# Run docker server with dind

```bash
docker run \
  --name jenkins-server \
  --rm \
  --detach \
  --privileged \
  --network jenkins \
  --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 \
  docker:dind \
  --storage-driver overlay2
```

# Build docker client image

### amd64 Dockerfile

```jsx
FROM jenkins/jenkins:2.277.2-lts-jdk11
USER root
RUN apt-get update && apt-get install -y apt-transport-https \
       ca-certificates curl gnupg2 \
       software-properties-common
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
RUN apt-key fingerprint 0EBFCD88
RUN add-apt-repository \
       "deb [arch=amd64] https://download.docker.com/linux/debian \
       $(lsb_release -cs) stable"
RUN apt-get update && apt-get install -y docker-ce-cli
USER jenkins
RUN jenkins-plugin-cli --plugins "blueocean:1.24.6 docker-workflow:1.26"
```

### arm64 Dockerfile (Apple Silicon)

```jsx
FROM jenkins4eval/jenkins:slim-arm64
USER root
RUN apt-get update && apt-get install -y apt-transport-https \
    ca-certificates curl gnupg2 \
    software-properties-common
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
RUN apt-key fingerprint 0EBFCD88
RUN add-apt-repository \
    "deb [arch=arm64] https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable"
RUN apt-get update && apt-get install -y docker-ce-cli
USER jenkins
RUN install-plugins.sh antisamy-markup-formatter matrix-auth blueocean:1.24.6 docker-workflow:1.26
```

### Build custom jenkins client image

```jsx
docker build -t jenkins-client:1.1 .
```

# Run docker client with blueocean plugin

```jsx
docker run \
  --name jenkins-client \
  --rm \
  --detach \
  --network jenkins \
  --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client \
  --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 \
  --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  jenkins-client:1.1
```

# Run with docker compose

# Jenkins Notes

### User Access

- Get admini initial password

```jsx
docker exec jenkins-blueocean-client cat /var/jenkins_home/secrets/initialAdminPassword
```

### Reset docker volumens

1. Remove jenkins containers.
2. Remove docker volumes (docker volume prune).