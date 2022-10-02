# Catatan

```
docker network create submission-dicoding
```

```
docker run --name jenkins-docker-submission --detach ^
  --privileged --network submission-dicoding --network-alias docker ^
  --env DOCKER_TLS_CERTDIR=/certs ^
  --volume jenkins-docker-submission-certs:/certs/client ^
  --volume jenkins-data-submission:/var/jenkins_home ^
  --publish 3000:3000 --publish 2376:2376 ^
  docker:dind
```

```dockerfile
FROM jenkins/jenkins:2.346.1-jdk11
USER root
RUN apt-get update && apt-get install -y lsb-release
RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
  https://download.docker.com/linux/debian/gpg
RUN echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
  https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
RUN apt-get update && apt-get install -y docker-ce-cli
USER jenkins
RUN jenkins-plugin-cli --plugins "blueocean:1.25.5 docker-workflow:1.28"
```

```
docker build -t myjenkins-blueocean:2.346.1-1 .
```

```
docker run --name jenkins-blueocean-submission --detach ^
  --network submission-dicoding --env DOCKER_HOST=tcp://docker:2376 ^
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 ^
  --volume jenkins-data-submission:/var/jenkins_home ^
  --volume jenkins-docker-submission-certs:/certs/client:ro ^
  --volume "%HOMEDRIVE%%HOMEPATH%":/home ^
  --restart=on-failure ^
  --env JAVA_OPTS="-Dhudson.plugins.git.GitSCM.ALLOW_LOCAL_CHECKOUT=true" ^
  --publish 49000:8080 --publish 50000:50000 myjenkins-blueocean:2.346.1-1
```

Credentials:

- username: admin
- password: admin
- e-mail : admin@admin.com

- username: dicoding
- password: dicoding
- e-mail : dicoding@dicoding.com

- submission-cicd-pipeline-djevannn

### Allow User to Sign Up

Manage Jenkins > Configure Global Security > Centang **Allow users to sign up**
