# Dockerizing Jenkins 2

This is a guide for dockerizing jenkins. Using an ittertive proccess, you can follow and repeat these steps:

  1. Set up manually.
  2. Set up programmatically.
  3. Automate with Docker.

This will allow you to build on the default Jenkins docker image.

## Overview 

The steps we need to setup up build a pipeline for a Java project: 

  * Pull the code from scm.
  * Configuration of Java and Maven.
  * Running unit tests.
  * Running static analysis.
  * Sending report to SonarQube for further processing.
  * Deployment of the .jar file to the repository.
  * New release it after each commit.

Once the docker image is customised to you liking the above will work OOTB (Out Of The Box) and be repeatable anywhere.

## Manual Setup

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

Next, dockerizing the Jenkins plugin installation part now by creating an empty file called [Dockerfile](Dockerfile). 


Now to build the docker image:

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


### Tool Installation 

TODO....

### Resources 

Dzone's [Dockerizing Jenkins 2, Part 1: Declarative Build Pipeline With SonarQube Analysis](https://dzone.com/articles/dockerizing-jenkins-2-setup-and-using-it-along-wit)
