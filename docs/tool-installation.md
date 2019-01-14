# Tool Installation

First, create a _downloads_ folder and then for Jenkins to run anywhere, we need to download the tools we need.

### Java

```
curl -O http://ftp.osuosl.org/pub/funtoo/distfiles/oracle-java/jdk-8u131-linux-x64.tar.gz
curl -O http://ftp.osuosl.org/pub/funtoo/distfiles/oracle-java/jdk-7u76-linux-x64.tar.gz
```

### Maven

```
curl -O http://apache.mirror.anlx.net/maven/maven-3/3.5.0/binaries/apache-maven-3.5.0-bin.tar.gz
```

Your downloads directory should look like this:
```
downloads
├── apache-maven-3.5.0-bin.tar.gz
├── jdk-7u76-linux-x64.tar.gz
└── jdk-8u131-linux-x64.tar.gz
```

We are now ready to run the container again, but this time with a mounted _downloads_ directory, like so:

```
$ docker run -p 8080:8080 -v `pwd`/downloads:/var/jenkins_home/downloads --rm --name myjenkins myjenkins:latest
```

**NB.** - To get access to the _/downloads_ folder, modify the permissions to the directory, ie: 
```
$ chmod 775 downloads/
```

**NB.** - The -v option in the above command, this relates to *Volume* that is to be mounted. 

To check the voulume is mounted and the files are there, run the following docker comand:
```
$ docker exec -it myjenkins ls -l /var/jenkins_home/downloads
```

**NB.** - This did not work for me, so I created a workaround and copied accross the files:
```
$ docker cp $(pwd)/downloads/apache-maven-3.5.0-bin.tar.gz myjenkins:/var/jenkins_home/downloads
$ docker cp $(pwd)/downloads/jdk-7u76-linux-x64.tar.gz myjenkins:/var/jenkins_home/downloads
$ docker cp $(pwd)/downloads/jdk-8u131-linux-x64.tar.gz myjenkins:/var/jenkins_home/downloads
```

To add to Jenkins Global Tool Configuration Manually:-

###jdk8
file:/var/jenkins_home/downloads/jdk-8u131-linux-x64.tar.gz
jdk1.8.0_131

###jdk7
file:/var/jenkins_home/downloads/jdk-7u76-linux-x64.tar.gz
jdk1.7.0_76

###maven3
file:/var/jenkins_home/downloads/apache-maven-3.5.0-bin.tar.gz
apache-maven-3.5.0


## Automate tool installation 
Now lets automte that by adding a */groovy* folder with scripts to automaticlly add Java and Maven. 

#### Java
```
import hudson.model.JDK
import hudson.tools.InstallSourceProperty
import hudson.tools.ZipExtractionInstaller
def descriptor = new JDK.DescriptorImpl();
def List<JDK> installations = []
javaTools=[['name':'jdk8', 'url':'file:/var/jenkins_home/downloads/jdk-8u131-linux-x64.tar.gz', 'subdir':'jdk1.8.0_131'],
      ['name':'jdk7', 'url':'file:/var/jenkins_home/downloads/jdk-7u76-linux-x64.tar.gz', 'subdir':'jdk1.7.0_76']]
javaTools.each { javaTool ->
    println("Setting up tool: ${javaTool.name}")
    def installer = new ZipExtractionInstaller(javaTool.label as String, javaTool.url as String, javaTool.subdir as String);
    def jdk = new JDK(javaTool.name as String, null, [new InstallSourceProperty([installer])])
    installations.add(jdk)
}
descriptor.setInstallations(installations.toArray(new JDK[installations.size()]))
descriptor.save()
```
#### Maven
```
import hudson.tasks.Maven
import hudson.tasks.Maven.MavenInstallation;
import hudson.tools.InstallSourceProperty;
import hudson.tools.ToolProperty;
import hudson.tools.ToolPropertyDescriptor
import hudson.tools.ZipExtractionInstaller;
import hudson.util.DescribableList
import jenkins.model.Jenkins;
def extensions = Jenkins.instance.getExtensionList(Maven.DescriptorImpl.class)[0]
List<MavenInstallation> installations = []
mavenToool = ['name': 'maven3', 'url': 'file:/var/jenkins_home/downloads/apache-maven-3.5.0-bin.tar.gz', 'subdir': 'apache-maven-3.5.0']
println("Setting up tool: ${mavenToool.name} ")
def describableList = new DescribableList<ToolProperty<?>, ToolPropertyDescriptor>()
def installer = new ZipExtractionInstaller(mavenToool.label as String, mavenToool.url as String, mavenToool.subdir as String);
describableList.add(new InstallSourceProperty([installer]))
installations.add(new MavenInstallation(mavenToool.name as String, "", describableList))
extensions.setInstallations(installations.toArray(new MavenInstallation[installations.size()]))
extensions.save()
```

Update your docker file to copy accross the groovy scripts. 
```
#adding scripts
COPY groovy/* /usr/share/jenkins/ref/init.groovy.d/
```

Make sure to stop and rebuild your docker image now. 

Resart the container by running the following:
```
docker run -p 8080:8080  -v `pwd`/downloads:/var/jenkins_home/downloads -v `pwd`/jobs:/var/jenkins_home/jobs/ --rm --name myjenkins myjenkins:latest
```

!!!!! Check the test_pipeline to see if installed correctly ?????

Also jobs/ folder.... keep the jobs data???


### Referances

[Copy file from host machine to docker container](https://til.codes/copy-file-from-host-machine-to-docker-container/)

[Back To Home](https://github.com/Bryn-Lloyd/dockerized-jenkins)
