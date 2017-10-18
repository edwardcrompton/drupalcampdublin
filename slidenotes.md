Set Up
------
- Open slides in browser:
file:///home/edward/Documents/DrupalCampDublin/drupalcampdublin/index.html

- Set lock screen on phone to 30 mins

- Open notes on phone:
https://github.com/edwardcrompton/drupalcampdublin/blob/gh-pages/slidenotes.md

- Open a single terminal at:
/home/edward/Documents/DrupalCampDublin/drupalcampdublin/drupal

- Ensure docker-compose up -d runs

- Ensure I can navigate to localhost:1234



Slide 0
-------

This is a brief introduction to Docker concepts for those of us unfashionably late to the Docker party.

Slide 1
-------

Been told for a while that virtual machines are old hat.

Fundamental concepts are a barrier.

Fully realising the advntages of containerisation means grasping some central concepts.

I'm going to take you through FOUR of the important realisations I've had.

Hopefully provide you with a good foundation to start exploring Docker as a solution for your own Drupal development and deployment.

Slide 2
-------

Docker utilises Linux 'containers'.

Redhat says this.

Containers package a runtime environment
Include all files necessary to run
Move an application between environments while retaining full functionality.

So far containers sound a lot like Virtual Machines.

Slide 3
-------

So... what's different about containers?

Containers are designed to run a process in an isolated, controlled environment and then exit.

Containers don't contain an entire operating system. They're therefore fast and require few resources to run.

Slide 4
-------

docker pull
Pull an image from the public remote docker repository by name. We can then use that image as the basis for a container.
In the docker world, the remote public repo that houses docker images is called Docker Hub.

docker ps
List the current docker containers on the system with their status: Up, Exited

docker run
Create a docker container based on an image and run a command in it.

Slide 5
-------

First we're going to pull an image called 'alpine' from the remote public repo of docker images. Alpine container some basci linux commands to run in our container.
> docker pull alpine

Next we'll run a command to list all current containers. You can see that there aren't any.
> docker ps -a

Let's run a short command.
> docker run alpine echo "Bono wants his hat back"
The string is echoed and you can see that it ran.

Now we'll take a look at those docker containers again. You can see that there is one, but it's status is Exited.
> docker ps -a

We ran echo in the container and it exited.

Slide 6
-------

Let's try running a process that won't exit straight away: crond -f

> docker run -d alpine dmesg --follow
We used the -d switch to detach the terminal from the docker proccess, but the long hash is a identifier for the container created.

> docker ps -a
See it running

This means that when it comes to Drupal, we expect to have a continuously running nginx container, but the container holding drush will only have a lifetime as long as the drush command you're currently running.

Slide 7
-------

* Ah ha moment 1.
Containers are ephemeral: The lifetime of the container is the same as the lifetime of the process.

The picture of the cobwebby lightbulb is a good representation of my moment of inspiration, I thought.


Slide 8
-------

Containers are namespaced. This means that processes running in one container cannot see processes running in another container.

This is how they're described on the hackernoon blog.

https://hackernoon.com/the-curious-case-of-pid-namespaces-1ce86b6bc900

Namespacing is responsible for the isolation of the container: filesystem, process, network. 

Slide 9
-------

Let's have a quick demo of that. If we run ps inside a container we only see the processes in that container.

In this case just a single root process.

Slide 10
--------

* Ah ha moment 2.
It's this separation based on namespace that is the primary feature of a container. Processes run in isolated environments. They also have isolated filesystems.

Slide 11
--------
 
So, let's try doing something useful: Nginx in a container, detached from our terminal.

> docker pull nginx
Pull the nginc image from dockerhub

> docker run -d -p 1234:80 nginx
Run a container using that image, detached and with port 1234 locally mapped to port 80 (nginx's default port)

> lynx localhost:1234
View the nginx welcome page.

> docker stop 
Stop the container

> lynx localhost:1234
Check we can't access it anymore

Slide 12
--------

* Ah ha moment 3.
Containers should perform one role. They may run more than one process, but they should not have more than one role.

To build anything but an extremely simple system we need several Docker containers interacting with each other.

Some advantages of splitting a web application like Drupal up into parts are:

Security
Modularisation

In that way Docker follows the UNIX philosophy of "doing one thing and doing it well".

But most applications, including Drupal require a whole load of processes: nginx, database, drush, solr search etc etc etc.

Slide 13
--------

Up until now we've looked only at controlling individual containers at the command line.

A lot of books spend a lot of time introducing docker concepts using the docker run command.

I spent a long time wondering how I was supposed to master docker run for multi containers.

Slide 14
--------

This takes us on to the fourth "ah ha!" moment: 
A tool called docker-compose allows us to work with multicontainer systems. Without these, acheiving anything useful with docker run commands can seem like an intimidating task!

Slide 15
--------

Let's look at how we can control several Docker containers at a higher level that constitute a working system.

docker-compose is the tool for this.

docker-compose is a separate application used for running multicontainer docker applications.

docker-compose takes its orders from a docker-compose.yml file.


Slide 16
--------

docker-compose.yml for a Drupal installation.

services: List the containers that we want to have running in our system
web: The nginx container
image: The name of the image to use. We need more than just the official nginx image because we need PHP too.
volumes: This is a way of mapping a folder from outside the container to a location inside the container. In this case we're mapping our Drupal
codebase to the nginx root.
ports: we need these too. Map 1234 locally to port 80 on nginx

database: This is the name of the database container
image: We're just going to use a standard mariadb image
environment: allows us to add environment variables. We can add a username and database name here that will get created when the container is built. However, I'm just being lazy here and specifying a mysql root password which is the minimum we need.

Slide 17
--------

Demo docker-compose ?live? and show drupal site.

Slide 18
--------

Summary:

The four concepts we've learned.

Containers are ephemeral and should be disposable. Design your system so that the stuff you need to keep is stored outside your containers.
Containers are part of your host system but isolated.
Keep the Unix philosophy in mind when you're designing your containers. Do one thing well.
Use docker-compose to build useful multicontainer systems.

Slide 19
--------

Mention that there's a Drupal docker image already. 

Recommended reading

networks
getting xdebug working

Suggest further discussion
