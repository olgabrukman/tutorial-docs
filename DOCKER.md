#Docker

[Docker interactive course](https://www.katacoda.com/courses/docker)

##What containers are and how they are different from VMs.
**VM=packaged OS running on [hyperviser+hardware] ~ physical machine.**
Operating-system-level virtualization is a server virtualization method in which the kernel of an operating system allows the existence of multiple isolated user-space instances, instead of just one. Such instances, which are sometimes called containers, software containers, virtualization engines (VEs) or jails (FreeBSD jail or chroot jail), may look and feel like a real server from the point of view of its owners and users.
On Unix-like operating systems, this technology can be seen as an advanced implementation of the standard chroot mechanism. In addition to isolation mechanisms, the kernel often provides resource-management features to limit the impact of one container's activities on other containers. Containers are not aware of each other.

**Container=packaged [applications+tools], server virtualization on top of OS.** 
Containers are much faster than VMs. 
 
##Docker images. 
*Image ID, namespace, name and tag; top-level namespace, latest tag. Container name and ID. Image registry(image name-link to image repository location mapping) vs. image repository (storing images). Public images are stored on DockerHub. 
*Container has an image. image id = image hash (you may have images with different names but with the same content, these images will have identical ids).Images registry - collection of off-the-shelf images.
*Pulling images. Image search. Managing local images (images, rmi).
*Creating a Docker container. Two modes — interactive (-ti) and daemon (-d). Running on a newly especially created container (run), executing commands (e.g. opening a shell session) on a running container (exec), listing (ps [-a]), and terminating (kill) containers. 
*Running a command on a container is making the container to execute the command and terminate. The container is continuously up if it has a daemon  process/application. Executing a command in daemon mode, while a container does not have a daemon process, will result in executing the command and exiting.
Executing a command in interactive mode will open user input prompt, asking for user input. 
*Size of Docker images. The concept of layers. Image visualization with ImageLayers.
*Building custom Docker images from modified containers (commit) and using Dockerfile (build). Explicitly defined build files. 
*Entry point. Packaging a Java application as a Docker image. We will expose the token counter as a Web service using Spring Boot, and run it in a Web container.
*Pushing images into a central repository. 
*How containers can be used in development, as well as for fast and painless integration testing even if the application is not intended to run in a container. Maven plugin for building Docker images.
*Container runtime, storage, ports and networking. Using docker inspect. Viewing container logs.
*Passing environment variables during build and on startup.
