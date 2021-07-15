What is Containerization?
=========================
AS described by creators:
"Docker provides the ability to package and run an application in a loosely isolated environment called a container. The isolation and security allow you to run many containers simultaneously on a given host. Containers are lightweight because they don’t need the extra load of a hypervisor, but run directly within the host machine’s kernel. This means you can run more containers on a given hardware combination than if you were using virtual machines"

# Image
The image is a blueprint of the container and the container is created from it.
For simplicity, if you take this from an object-oriented programming point of view, an image is a class, where all the requirements are defined and declared. A container is an instance of the image. These images are stored somewhere in the cloud and pulled as needed

# Container
A container is an instance of an image, which simulates the required environment with the use of the Linux kernel packaged in it. In the diagram, you can see app B is enclosed in one container. Similarly, you can enclose the other two apps as well

# Docker ecosystem

* Docker Registry: Docker maintains all the images in the registry and they can be pulled from the registry too

* Docker Hub: This is the repository for all your custom-built images. Images can be pushed and accessed from the Hub

* Docker Client: The CLI tool used to interact with the Docker server

* Docker Daemon: The Docker server process responsible for pulling, pushing, and building the images. It is also used for running the container

Throughout this course, we will be using a local Docker client and daemon only. There are only a few things you need to keep in mind while working with Docker:

1. Images
2. Containers
3. Networks - which we will be cover in the Docker Compose section

The short list above is all you need. Docker takes care of the rest. There are so many abstractions in the implementation of Docker for every platform other than Linux, but the whole idea remains the same: providing the native Linux kernel to containers. So, let’s focus on the core functionality we need to do development and let the Docker daemon do the rest

## Installation

```bash
# Remove any older versions of docker
# use sudo if required
$ sudo apt-get remove docker docker-engine docker.io containerd runc
# Update package index
# use sudo if required
$ sudo apt-get update
# Use sudo if required
$ sudo apt-get install apt-transport-https ca-certificates curl \
    gnupg-agent \
    software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
$ sudo apt-get update
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
$ sudo docker info
```
# if  you don't want to use sudo with all docker commands. 
```bash
$ sudo usermod -aG docker <your-user>
```

## Hello world in docker
`$ docker run hello-world`
```bash
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

If you get the output above, all is set. If you read the sentences below, line by line, you will be able to recall what we have learned in the architecture lesson.

* Docker daemon first tries to find the image locally.
* It pulls the image from the registry.
* Docker daemon runs the image.
```bash
docker image ls             # list all the images present on the system
docker ps                   # list all the running images on the system 
```
Note: you can see that nginx occupies 128MB while hello-world image 13kB. Note that if there were no component sharing the program would have occupied lot more space. 
As every image is built on top of Linux kernel, it has some common dependencies that can be reused by other images. Docker bundles these dependencies in one stack and these stacks are called layers.

## Commands used in this lesson #
```bash
docker pull \<images-name>:\<version> : pulls image from Docker registry
docker run \<images-name>:\<version> : runs container from mentioned image
docker ps : shows all running containers
docker ps -a: shows all available containers
docker exec: executes a command in a running container
```
```bash
$ docker pull python:3.5         # will pull from the registry if not present locally
$ docker run python:3.5          # will 'fork' the container can be seen from following command; docker ran and exited
$ docker ps -a
CONTAINER ID   IMAGE               COMMAND                  CREATED              STATUS                          PORTS     NAMES
0d225fd5d79f   python:3.5          "python3"                About a minute ago   Exited (0) About a minute ago             infallible_lumiere
```

**CONTAINER ID**: shows the unique ID of each container

**IMAGE**: the image from which the container is created

**COMMAND**: command executed in the container while starting it

**CREATED**: the time the container was created

**STATUS**: the current status of the container

**PORTS**: if any of the container ports is connected to the host machine, it will be displayed here

**NAMES**: this is the name of a container. If it is not provided while creating the container, Docker provides a unique name by default.

Type 
```bash
$ docker run -it <image name> <command to execute>
```
To get the shell, we will execute bash as a command. The options i & t are used to provide an interactive mode and a pseudo tty terminal.
```bash
$ docker run -it bash 
root@1a423eda20e2:/# whoami
root
```
Currently, we are in a virtual Linux OS. It is very lightweight as no extra packages will be installed in it except Python.
You can get back to the bash of any running container anytime you want by running 
```bash
$ docker exec -it <container id/name> bash      # note that the container must be already running, does not work for exited containers
```
To start an exited/stopped container: 
```bash
$ docker start <container_id>
$ docker exec <container_id/name> <comman>      # runs the container again
$ docker kill <container_id>                    # kills the container (docker ps will not have that  container_id anymore and ps -a will show exited
root@asdfks234:/# apt update
root@asdfks234:/# apt install vim               # i think it does not download, if possible it shares from the ubuntu/linux kernel. 
```

### Note

```bash
$ docker run python:3.5 bash                    # does not show any output but creates a new container(forks the image python:3.5)
$ docker run container_id                       # is not valid (run must have an image as an argument) not a container => error
$ docker run -it python:3.5 bash                # again forks a new container and will show you a bash terminal
$ docker exec -it <container_id/name> bash      # if the container is already running then it starts the bash using that else , error
$ docker start <container_id/name>              # start the container/ make the container running, now you can exec a bash
```

### Why do we need to commit?
One goal of containers is to be able recreate the environement with all the depedencies without much of a headache. 
This is acheived by creating an image out of the changes that we have made into our container. We make these changes
by commiting it.
### How to commit?
```bash
$ exit                                          # from the current container's bash if inside it. 
$ docker commit -m '<commit message>" <container_id/name> <new_image_name>:<version>
$ docker  commit -m "add date showing program to python3.5" 1a423eda20e2 date_shower:0.1
```
## Push to docker hub
```bash
$ docker login
$ docker tag <image_name>:<version> <docker_username>/<image_name>:<version>
$ docker push <image_name>:<version> <docker_username>/<image_name>:<version>
```
## Connecting host machine file system to container
Whenever you create a container from an image, it creates a new container without any data except the image data. We created the date-project image, which is a very small image. It has only one project file which we created in the container file system. If the container is removed before committing the changes, we will lose the data. So, it is always good practice to separate your data’s file system from the container’s file system.
* docker volume --help: to get the volume help
* docker volume create: to create a new volume
* docker inspect volume: to inspect the created volume
* docker run -v: to mount a volume
### Bind mount
Here you bind a path on host to the some directory in the container. 
```bash
$ docker run -it -v /home/tesla/Desktop/:/python_desktop date_project:0.1           # exec does not work
```
__`To mount the file system as read-only, use ro flag. $docker run -it -v <absolute_path>:<folder path or new folder name>:ro date_project:1.0`__

### Volumes
Docker volumes are mostly created to share data within different containers, rather than sharing data with host and container
`docker volume --help`
```bash
$ docker volume create <volume_name>                        # if no name is provided docker provides a unique name 
```
```bash
$ docker volume inspect new_volume                          # inspect a new volume
[
    {
        "CreatedAt": "2021-07-15T20:18:19+05:30",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/new_volume/_data",
        "Name": "new_volume",
        "Options": {},
        "Scope": "local"
    }
]
```
```bash
$ docker run -it -v new_volume:/project date_shower:0.1         # while using 'run' what ever is after the image_name will be the command run in the container
```

## Some useful commands

Command	Action
`
docker ps	                        Lists all running containers. -a option will list stopped and running both
docker inspect [container_name]	    Provides all info about the container
docker stop [container_name]	    Stops the running container
docker kill [container_name]	    Kills(stops) the container and removes the container from the system
docker rmi [image/s]	            Removes the provided image
docker images	                    Lists all images on the system
docker exec [-it]	                Executes command in a Docker container
docker system	                    Gets the Docker system information such as memory usage and housekeeping stuff
docker system prune	                This command will save you from getting the “No memory left” nightmare with production systems
`

## Problem for practise:
1. pull the image for python3.8 and keep track of the layers it has fetched. 
```bash
$ docker pull python:3.8
```
There are 9 layers that were pulled. 

2. Create a docker volume named app_files
```bash
$ docker volume --help                          # try to use help
$ docker volume create app_files                # One has to be able remember 'create' comman
```

3. Create a container named 'first_container' from the Python3.8 image with app_files volume attaced to it. 
```bash
$ docker run -it -v app_files:/folder_in_container --name first_container python:3.8 bash       # you may not start bash
```

4. Write a Python program named 'current_time.py' to display the current timestanp inside the app_files volume. 
```bash
$ docker start first_container
$ docker exec -it first_container bash              # open the a file and create the python code for current time. 
```

5. Create another container named 'second_container' from the python 3.8 image and update the python script located in the app_files volume to print the current date. 
```bash
$ docker run -it -v app_files:/to_second_container --name second_container python:3.8 bash
```
Now go the shared folder, and chaneg th code such that the time is shown separtely

## Dockerfile
### Understanding a bit of flask
## Running the app on a Mac/Linux machine 
Most of the time, Python 3 comes prebuilt with these platforms. If yours isn’t, install Python 3 using this link.

## Steps to run 
The steps here are similar to what we used with a Windows machine, except the command to activate your virtual environment.

* Open a terminal
```bash
$ git clone url_to_repo
$ git checkout to_particular_hash_commit
type python3 -m venv myenvname
type 
$ source <venv>\bin\activate
$ git clone https://github.com/venky8283/Docker.git
$ cd Docker
$ pip install -r requirements.txt
$ export FLASK_APP=app.py set this to let Flask know where the Flask instance is located (optional: if you have a ‘.flaskrun’ file with this variable).
$ flask run
```
Once you are done, you should be able to access the app on ‘http://localhost:5000’.

**Running so many commands every time you set a project is cumbersome, plus these steps will increase once the project gets bigger. Also, remembering different commands for different platforms is difficult. That’s where Docker comes to the rescue.**

And Docker does so by **Dockerfile**.

1. git clone repo_url
2. git checkout d469419582ee877067f6d449c9691ad16e5f3365
3. 
```bash
## Base Python Image for App
FROM python:3.9-rc-buster


# Setting up Docker environment
# Setting Work directory for RUN CMD commands
WORKDIR /code
# Export env variables.
ENV FLASK_APP app.py
ENV FLASK_RUN_HOST 0.0.0.0
###


#Copy requirements file from current directory to file in
#containers code directory we have just created.
COPY requirements.txt requirements.txt

#Run and install all required modules in container
RUN pip3 install -r requirements.txt

#Copy current directory files to containers code directory
COPY . .

#RUN app.
CMD ["flask", "run"]

```


##  Docker Commands
Docker isolates the code environment from the host machine using containers and containers are the instances of the image. In a programming language, this Dockerfile will provide us with a blueprint also called an image, from which we can run our app in a container.

Docker has some predefined keywords to use for providing commands. We can use one keyword in a line to provide one command at a time.

### FROM
The FROM keyword tells Docker which base image to use or what should be the main platform for this image.
The Python:3.9-rc-buster image is a Linux-based image which has Python installed and no other additional programs or software.

### WORKDIR
The WORKDIR instruction sets a working directory for other Docker instructions such as RUN and CMD.
If we do not specify a working directory, we have to provide a full path for running our app.py file while using the RUN instruction.

### ENV
The ENV instruction sets an environment variable required for the flask app. We can skip these ENV steps, but you have to provide those variables using .flaskenv file.
Here, we are setting host to 0.0.0.0 which means we can access the app using any IP within the container.
> Setting up a host to 0.0.0.0 is required if you are running the app inside the container and want to access it outside. If you change it to anything else, you won’t be able to access the app from the host machine.

COPY #
The COPY instruction literally copies the file from one location to another. COPY SOURCE DESTINATION is the syntax of the command.

Here, we are copying the requirements.txt file first so that we will have all the libraries installed before copying all the files.

RUN #
The RUN instruction will execute any commands in a new layer on top of the current image and commit the results. The resulting committed image will be used for the next step in the Dockerfile.

In the previous section, we learnt about layers. The RUN instruction creates a new layer here. It runs the commands provided as an argument in that layer.

One benefit of running requirements before copying all the files is whenever a change occurs in project files, we don’t have to build requirements again and Docker will use a cached layer for requirements.

CMD #
CMD runs the command inside the container once a container is forked or created from an image. You can only have one CMD instruction in a Dockerfile. If multiple CMD instructions are used, the last one will be executed.

Here, once the container is created, the CMD command will run our project.

We have one more frequently-used instruction.

ENTRYPOINT #
The ENTRYPOINT instruction can be used if you want to configure your container as an executable. If you want to override CMD while running a container, use ENTRYPOINT,

for example, ENTRYPOINT [“flask”, “run”]

These definitions are enough to start with Dockerfile. However, if you want to know more about these commands, checkout the official documentation.

Click on the run button above and access the app.

Build your app image #
Now, it’s time to build the image. First, cd into the directory where the Dockerfile is located.

> Type docker build -t flask_app:1.0 . .
> If you get any error, try sudo docker build -t flask_app:1.0 .
> The last ‘.’ in the command is very important. It denotes the location of the Dockerfile to use for building the image.

Let’s run our app in the next lesson. This will create an image on your local machine. You can check that using docker images.

`$ docker run flask_app:1.0            # we will not be able to see the result at 5000 port but the output on console
 * Serving Flask app 'app.py' (lazy loading)
 * Environment: development
 * Debug mode: on
 * Running on all addresses.
   WARNING: This is a development server. Do not use it in a production deployment.
 * Running on http://172.17.0.4:5000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 705-752-825`

 You need to map the port of the container (**port mapping**)
 ```bash
$ docker run -p 5000:5000 flask_app:1.0
 ```







