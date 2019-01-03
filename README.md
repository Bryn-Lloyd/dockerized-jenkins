# Dockerizing Jenkins 2

This is a guide for dockerizing jenkins. Using an ittertive proccess, you can follow and repeat these steps:

  1. Set up manually.
  2. Set up programmatically.
  3. Automate with Docker.

This will allow you to build on the default Jenkins docker image.

## Overview 

The steps are needed to setup up a build pipeline for a Java project that include the following stages: 

  * Pull the code from scm.
  * Configuration of Java and Maven.
  * Running unit tests.
  * Running static analysis.
  * Sending report to SonarQube for further processing.
  * Deployment of the .jar file to the repository.
  * New release it after each commit.

Once the docker image is customised to requirment, the above will work OOTB (Out Of The Box) and be repeatable anywhere, for any deployment.

## First Steps

To build the docker image, first you will need to go over the following:

 * [Manual Setup](docs/maunual-setup.md)
 * [Tool Installation](docs/tool-installation.md)
 * 

### Tips and Tricks

Here are a couple of helpful pages:

 * [Common Docker Commands](docs/common-docker-commands.md)

## Summary

- what is this repositories purpose?

### Resources 

Dzone's [Dockerizing Jenkins 2, Part 1: Declarative Build Pipeline With SonarQube Analysis](https://dzone.com/articles/dockerizing-jenkins-2-setup-and-using-it-along-wit)
