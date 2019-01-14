# Manual Setup

Download the docker jenkins image:

```
$ docker pull jenkins:2.60.1
```

*NB* - This requires docker installed on your local machine (share a link to docker).

Run the following command to see the downloaded docker image (link to common docker commands)

```
$ docker image ls
```

Run the container locally on your machine with the following command 

```
$ docker run -p 8080:8080 --rm --name myjenkins jenkins:2.60.1
```

*NB* - The ```-rm``` flag is added to remove the contain once it is nolonger being used. Running Jenkins on the fly like this will mean the all data will be lost, unless we explicitly say so. 

Using the admin password (from the Docker logs) sign in and use the Jenkins plugin installation wizard with the default set up. 

Then, to descover what pluggins were installed, run the following command:

```
docker exec -it myjenkins ls /var/jenkins_home/plugins/ | grep -v jpi
```

Once the list of plugimns has been obtained, you can then store them in a _plugins.txt_ file so that they can be installed when building the docker image. 

Stop the container:

```
docker stop myjenkins
```

Next, dockerizing the Jenkins plugin installation part now by creating an empty file called [Dockerfile](Dockerfile). Now to build the docker image:

```
docker build -t myjenkins .
```

You will now see the Jenkins container being built in the Docker logs. 

To run the new docker container, simply enter the following command:

```
docker run -p 8080:8080 --rm --name myjenkins myjenkins:latest
```

You now have a baked docker image that will download the predefined Jenkins plugins. 

Simply run the previous _list jenkins plugins_ step, to descover what pluggins were installed:

```
docker exec -it myjenkins ls /var/jenkins_home/plugins/ | grep -v jpi
```

**NB.** - The image [used in this page](https://hub.docker.com/_/jenkins/) has been deprecated in favor of the [jenkins/jenkins:lts](https://hub.docker.com/r/jenkins/jenkins) image provided and maintained by Jenkins Community as part of project's release process.

[Back To Home](https://github.com/Bryn-Lloyd/dockerized-jenkins)