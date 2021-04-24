- Create custom image of Nexus3 using nexus.properties

```jsx
nexus.scripts.allowCreation=true
nexus.security.randompassword=false
```

```jsx
# pulling offical image
FROM sonatype/nexus3
#copy configuration file to in docker container
COPY nexus.properties nexus-data/etc/nexus.properties
```

- Build Sonatype Nexus image (buildx for Mac M1 compatibility)

```jsx
docker build -t nexus-local:1.0.0 .
```

- Run Sonatype Nexus image in docker

```jsx
docker run -d -p 8081:8081 --name nexus-local nexus-local:1.0.0
```

## ARM64 version

- Build Nexus customized image. Using arm64 version from klo2k (klo2k/nexus3).

```jsx
# pulling klo2k image
FROM klo2k/nexus3
#copy configuration file to in docker container
# COPY nexus.properties nexus-data/etc/nexus.properties
```

```jsx
docker build -t docker.io/inimeve/nexus-local-arm64:1.0.0 .

docker push docker.io/inimeve/nexus-local-arm64:1.0.0
```

```jsx
docker run -d -p 8002:8081 --name nexus-local-arm64 docker.io/inimeve/nexus-local-arm64:1.0.0
```

## Access info

- Get admin initial password

```jsx
docker exec -it <container_name_or_ID> cat /nexus-data/admin.password
```

Default User: admin