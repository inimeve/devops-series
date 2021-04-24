- Build jenkins image

```jsx
docker build -t docker.io/inimeve/jenkins-local:1.0.0 .
```

- Deploy jenkins image

```jsx
docker run -d -p 8001:8080 -p 50000:50000 --name jenkins-local docker.io/inimeve/jenkins-local:1.0.0
```

## ARM64 version

- Build Jenkins customized image. Using arm64 version from jenkins4eval (jenkins4eval/jenkins:slim-arm64).

```jsx
FROM jenkins4eval/jenkins:slim-arm64

USER root

# Add the docker binary so running Docker commands from the master works nicely
RUN apt update
RUN apt install -y apt-transport-https ca-certificates curl gnupg2 software-properties-common
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
RUN add-apt-repository \
    "deb [arch=arm64] https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable"
RUN apt-get update && apt-get install -y docker-ce-cli

USER jenkins

RUN install-plugins.sh antisamy-markup-formatter matrix-auth blueocean:1.24.6
```

```jsx
docker build -t docker.io/inimeve/jenkins-local-arm64:1.0.0 .

docker push docker.io/inimeve/jenkins-local-arm64:1.0.0
```

```jsx
docker run -d -p 8001:8080 -p 50000:50000 --name jenkins-local-arm64 docker.io/inimeve/jenkins-local-arm64:1.0.0
```
