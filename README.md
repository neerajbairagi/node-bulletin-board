# Part:1

# Docker concepts

Docker is a platform for developers and sysadmins to build, run, and share applications with containers. The use of containers to deploy applications is called containerization. Containers are not new, but their use for easily deploying applications is.

# Containerization is increasingly popular because containers are:

    Flexible: Even the most complex applications can be containerized.
    
    Lightweight: Containers leverage and share the host kernel, making them much more efficient in terms of system
    resources than virtual machines.
    
    Portable: You can build locally, deploy to the cloud, and run anywhere.
    
    Loosely coupled: Containers are highly self sufficient and encapsulated, allowing you to replace or upgrade one
    without disrupting others.
    
    Scalable: You can increase and automatically distribute container replicas across a datacenter.
    
    Secure: Containers apply aggressive constraints and isolations to processes without any configuration required on
    the part of the user.


# Images and containers

Fundamentally, a container is nothing but a running process, with some added encapsulation features applied to it in order to
keep it isolated from the host and from other containers. One of the most important aspects of container isolation is that each container interacts with its own private filesystem; this filesystem is provided by a Docker image. An image includes everything
needed to run an application - the code or binary, runtimes, dependencies, and any other filesystem objects required.

# Containers and virtual machines

A container runs natively on Linux and shares the kernel of the host machine with other containers. It runs a discrete process,
taking no more memory than any other executable, making it lightweight.

By contrast, a virtual machine (VM) runs a full-blown “guest” operating system with virtual access to host resources through a hypervisor. In general, VMs incur a lot of overhead beyond what is being consumed by your application logic.

https://github.com/sonulodha/node-bulletin-board/blob/master/vm_docker.JPG

# Set up your Docker environment

# Test Docker version

After you’ve successfully installed Docker Desktop, open a terminal and run docker --version to check the version of Docker
installed on your machine.

$ docker --version

# Test Docker installation

  Test that your installation works by running the hello-world Docker image:

  $ docker run hello-world



Run docker image ls to list the hello-world image that you downloaded to your machine.

List the hello-world container (spawned by the image) which exits after displaying its message. 
If it is still running, you do not need the --all option:
$ docker ps --all

# Part: 2

# Introduction

Now that you’ve set up your development environment, thanks to Docker Desktop, you can begin to develop containerized applications.
In general, the development workflow looks like this:

    Create and test individual containers for each component of your application by first creating Docker images.
    Assemble your containers and supporting infrastructure into a complete application.
    Test, share, and deploy your complete containerized application.

In this stage of the tutorial, let’s focus on step 1 of this workflow: creating the images that your containers will be based on. Remember, a Docker image captures the private filesystem that your containerized processes will run in; you need to create an image that contains just what your application needs to run.

    Containerized development environments are easier to set up than traditional development environments, 
    once you learn how to     build images as we’ll discuss below. This is because a containerized development 
    environment will isolate all the dependencies     your app needs inside your Docker image; there’s no need
    to install anything other than Docker on your development machine. In this way, you can easily develop
    applications for different stacks without changing anything on your development machine.



# Git

 If you are using Git, you can clone the example project from GitHub:

git clone https://github.com/dockersamples/node-bulletin-board

cd node-bulletin-board/bulletin-board-app

The node-bulletin-board project is a simple bulletin board application, written in Node.js. In this example, let’s
imagine you wrote this app, and are now trying to containerize it. Define a container with Dockerfile

Take a look at the file called Dockerfile in the bulletin board application. Dockerfiles describe how to
assemble a private filesystem for a container, and can also contain some metadata describing how to run
a container based on this image. The bulletin board app Dockerfile looks like this:


# Use the official image as a parent image.
FROM node:current-slim

# Set the working directory.
WORKDIR /usr/src/app

# Copy the file from your host to your current location.
COPY package.json .

# Run the command inside your image filesystem.
RUN npm install

# Inform Docker that the container is listening on the specified port at runtime.
EXPOSE 8080

# Run the specified command within the container.
CMD [ "npm", "start" ]

# Copy the rest of your app's source code from your host to your image filesystem.
COPY . .


Writing a Dockerfile is the first step to containerizing an application. You can think of these Dockerfile commands as a 
step-by-step recipe on how to build up your image. This one takes the following steps:

    Start FROM the pre-existing node:current-slim image. This is an official image, built by the node.js 
    vendors and validated     by Docker to be a high-quality image containing the Node.js Long Term Support
    (LTS) interpreter and basic dependencies.
    
    Use WORKDIR to specify that all subsequent actions should be taken from the directory /usr/src/app in your
    image filesystem  (never the host’s filesystem).

    COPY the file package.json from your host to the present location (.) in your image (so in this case,
    to /usr/src/app/package.json)
    
    RUN the command npm install inside your image filesystem (which will read package.json to determine your
    app’s node dependencies, and install them)
    
    COPY in the rest of your app’s source code from your host to your image filesystem.

You can see that these are much the same steps you might have taken to set up and install your app on your host. However,
capturing these as a Dockerfile allows you to do the same thing inside a portable, isolated Docker image.

The steps above built up the filesystem of our image, but there are other lines in your Dockerfile.

The CMD directive is the first example of specifying some metadata in your image that describes how to run a container 
based on this image. In this case, it’s saying that the containerized process that this image is meant to support is 
npm start.

The EXPOSE 8080 informs Docker that the container is listening on port 8080 at runtime.

What you see above is a good way to organize a simple Dockerfile; always start with a FROM command, follow it with the 
steps to build up your private filesystem, and conclude with any metadata specifications. There are many more Dockerfile
directives than just the few you see above. For a complete list, see the Dockerfile reference


# Build and test your image

Now that you have some source code and a Dockerfile, it’s time to build your first image, and make sure the containers 
launched from it work as expected. 
Make sure you’re in the directory node-bulletin-board/bulletin-board-app in a terminal or PowerShell using the cd command. 
Let’s build your bulletin board image:

docker build --tag bulletinboard:1.0 .

You’ll see Docker step through each instruction in your Dockerfile, building up your image as it goes. If successful, the build
process should end with a message Successfully tagged bulletinboard:1.0.

docker images


# Run your image as a container

Start a container based on your new image:

docker run --publish 8000:8080 --detach --name app bulletinboard:1.0
docker run -p 8000:8080 -d --name app bulletinborad:1.0
  
Visit your application in a browser at localhost:8000. You should see your bulletin board application up and running. At this step,
you would normally do everything you could to ensure your container works the way you expected; now would be the time to run unit tests, for examp



# Share images on Docker Hub
At this point, you’ve built a containerized application in Part 2 on your local development machine. The final step in developing a containerized application is to share your images on a registry like Docker Hub, so they can be easily downloaded and run on any destination machine.
# If you don’t yet have a Docker ID, follow these steps to set one up; this will allow you to share images on Docker Hub.

    Visit the Docker Hub sign up page, https://hub.docker.com/signup.

    Fill out the form and submit to create your Docker ID.

    Verify your email address to complete the registration process.

    Click on the Docker icon in your toolbar or system tray, and click Sign in / Create Docker ID.

    Fill in your new Docker ID and password. After you have successfully authenticated, your Docker ID appears in 
    the Docker Desktop menu in place of the ‘Sign in’ option you just used.

        You can do the same thing from the command line by typing docker login.

# Create a Docker Hub repository and push your image

docker images

docker tag bulletinboard:1.0 mitrasonu/bulletinboard:1.0


docker push mitrasonu/bulletinboard:1.0

Visit your repository in Docker Hub, and you’ll see your new image there. Remember, Docker Hub repositories 
are public by default.
