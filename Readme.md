Marouane Abakarim
Web Developper


## Launch a Maven Java application with a postgre database in docker containers

# Create your first image using Java: 

Create the DockerFile containt the command build the image Docker.

**Create a new Java project:**

```sh
mvn archetype:generate -DgroupId=org.examples.java -DartifactId=helloworld -DinteractiveMode=false
```

**Build the project:**
```sh
cd helloworld
```
```sh
mvn package
```
**Run the Java class:**
```sh
java -cp target/helloworld-1.0-SNAPSHOT.jar org.examples.java.App
```

**This shows the output:**
```sh
Hello World!
```
Let’s package this application as a Docker image.

# Java Docker image:

Run the OpenJDK container in an interactive manner:
```sh
docker container run -it openjdk
```
This will open a terminal in the container. Check the version of Java:
```sh
root@8d0af9da5258:/# java -version
openjdk version "1.8.0_141"
OpenJDK Runtime Environment (build 1.8.0_141-8u141-b15-1~deb9u1-b15)
OpenJDK 64-Bit Server VM (build 25.141-b15, mixed mode)
```

A different JDK version may be shown in your case.

Exit out of the container by typing exit in the container shell.

# Package and run Java application as Docker image:

Create a new Dockerfile in helloworld directory and use the following content:
```sh
FROM openjdk:latest

COPY target/helloworld-1.0-SNAPSHOT.jar /usr/src/helloworld-1.0-SNAPSHOT.jar

CMD java -cp /usr/src/helloworld-1.0-SNAPSHOT.jar org.examples.java.App
```

**Build the image:**
```sh
docker image build -t hello-java:latest .
```
Run the image:
```sh
docker container run hello-java:latest
```
This displays the output:
```sh
Hello World!
```
This shows the exactly same output that was printed when the Java class was invoked using Java CLI.

# Installer PostgreSQL sur Docker:
```sh
FROM ubuntu:14.04
MAINTAINER Andrea Grandi <nospamthanks@gmail.com>

RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys B97B0AFCAA1A47F044F244A07FCC7D46ACCC4CF8
RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ precise-pgdg main" > /etc/apt/sources.list.d/pgdg.list

RUN apt-get update && apt-get -y -q install python-software-properties software-properties-common   
  && apt-get -y -q install postgresql-9.3 postgresql-client-9.3 postgresql-contrib-9.3
  
  USER postgres
RUN /etc/init.d/postgresql start   
  && psql --command "CREATE USER pguser WITH SUPERUSER PASSWORD 'pguser';"   
  && createdb -O pguser pgdb
  
  USER root
RUN echo "host all  all    0.0.0.0/0  md5" >> /etc/postgresql/9.3/main/pg_hba.conf
RUN echo "listen_addresses='*'" >> /etc/postgresql/9.3/main/postgresql.conf

EXPOSE 5432

RUN mkdir -p /var/run/postgresql && chown -R postgres /var/run/postgresql
VOLUME  ["/etc/postgresql", "/var/log/postgresql", "/var/lib/postgresql"]

USER postgres
CMD ["/usr/lib/postgresql/9.3/bin/postgres", "-D", "/var/lib/postgresql/9.3/main", "-c", "config_file=/etc/postgresql/9.3/main/postgresql.conf"]
```


#Building Docker image
Once the Dockerfile is ready, we need to build the image before running it in a container. Please customize the tag name using your own docker.io hub account (or you won't be able to push it to the hub):
```sh
docker build --rm=true -t andreagrandi/postgresql:9.3 .
```
Running the PostgreSQL Docker container
To run the container, once the image is built, you just need to use this command:
```sh
docker run -i -t -p 5432:5432 andreagrandi/postgresql:9.3
```
Testing the running PostgreSQL
To test the running container we can use any client, even the commandline one:
```sh
psql -h localhost -p 5432 -U pguser -W pgdb
```
When you are prompted for password, type: pguser
Please note that localhost is only valid if you are running Docker on Ubuntu. If you are an OSX user, you need to discover the correct ip using: boot2docker ip

# Persisting data:
....




