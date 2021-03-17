---
title: "Play With Docker"
date: 2021-03-16T02:11:57-04:00
draft: false
categories:
- Container
tags:
- docker
---

Docker has become a trend that every engineer would be familiar with, but most of the people would mistake docker for a kind of virtual machine. In this post, I would like to show the difference between container and virtual machine. Of course, I would like to help people to figure out how to play with docker in easier way.

## Difference between Container and Virtual machine
![](/3-16-21/diff-container-vm.png)  
With the picture above, you should find the difference between virtual machine and container. Virtual machine is based on OS, and Container is based on app. Therefore, container is not a created technique for replacing the virtual machine. Virtual machine helps us to simplize the steps of hardware settings and OS installation, and Container helps us to simplize the environment settings and automatic deployment of application. Hope this helps you and figure out the difference between virtual machine and container.

## Play with docker
I would like to show some important commands that you can practice with.  
Each docker can be shown with `username/repository:tag`.  
First, here come several basic commands for docker,  
```
docker images  -- show all images stored in localhost
docker ps   -- show information of running container
docker ps -a  -- same with previous command, but also including stopped container
docker rm   -- remove container
docker rmi  -- remove images
```
Basically, container would stop when it finish the intructions given by you. For example, if you try `docker run busybox echo 1`, this command would build image based on busybox, and run the command of `echo 1` in the container environment. `echo 1` is a very simple command which can be finished in soon so that you can see container in `exited` status immediately. However, we always want our container to run consistantly in background, so there are two feasible ways:  
1. `docker run -d usr/repo`
run a consistant service in container
2. `docker run -d img ping localhost`
manually run a consistant service  

![](/3-16-21/docker-d.png)  
Here you can see that `great_carson` runs the command of ping in container in the background. A recommended way is to use `docker run -d img /bin/bash`.  
Of course, you would like to **go into** the container to take a look, you might like to run `attach` command to do it.  
![](/3-16-21/docker-attach.png)  
Here you can see that I try to attach the ping service container. However, after I tried to exit the container, then the service and container also stopped by the way. The solution is to use `-td` option when running the command of `docker run`.  
![](/3-16-21/docker-attach-td.png)  
In the screenshot above, I tried to build up another container with `-td` option. This option means **container would continue working in background even we exit it**. This time, after I tried attach and exited it, `ps` told me that it still working in the background!  
There is another usually used command can help solve the problem: `exec`. This command would open another shell to run the command **from the outside**.  
![](/3-16-21/docker-exec.png)  
In screenshot above, I open another shell in container `test` and run the command `ps`. In my project, I always like to use `docker exec -it <name> bash -c "command"` to run the bash command from the outside. Option `it` helps me to see the results shown in container.  
We always like to use the images created by others. But how if we make some changes to the existing container and I want it to be saved as a new image? `docker commit <name> repo:tag` would be your friend! Following are the commands for giving your image as a file to others:  
```
# save the image as file
docker save usr:tag > xxx.tar
# load the image file into localhost
docker load xxx.tar   # then you can find it in your docker images
docker run -itd use:tag /bin/bash
```
Share with the man whom you like to give images to:)  

## Conclusion
Some interesting details I found while working with command `docker exec bash -c`. You need to pay attention to the fact that this command would start up a **new shell** on each time when called. Which case would be affected by this case? I set env variable in the previous command: `docker exec bash -c "export CC=clang"`, but `CC` would not work in next command because it would be different shell. Therefore, my solution is to use `docker exec bash -c` for only one time. I hope this post can help you learn docker in an easier way. Share or leave the comments if you find some more important commands for beginer. Hope you enjoy it.
