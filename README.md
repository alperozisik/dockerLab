# Task 0: Review-Recap

## Terminology
In this section, you will see a lot of Docker-specific jargon which might be confusing to some.
So, before you go further, let's clarify some terminology that is used frequently in the Docker ecosystem:

- **Images** - The file system and configuration of our application which are used to create containers. To find out more about a Docker image, run docker image inspect alpine. If you do not have the alpine image this command will fail. You can use the docker image pull command to download the alpine image. When you executed the command docker container run hello-world, it also did a docker image pull behind the scenes to download the hello-world image.
- **Containers** - Running instances of Docker images containers run the actual applications. A container includes an application and all of its dependencies. It shares the kernel with other containers, and runs as an isolated process in user space on the host OS. You created a container using docker container run based on an image that was downloaded. A list of running containers can be seen using the docker container ls command.
- **Docker daemon** - The background service running on the host that manages building, running and distributing Docker containers.
- **Docker client** - The command line tool that allows the user to interact with the Docker daemon.
- **Docker Hub** - A registry of Docker images. You can think of the registry as a directory of all available. Docker images. You'll be using this later in this tutorial.

# Task 1: Installing Docker

## Create Compute Instance
- 2 Core oCPU
- Ubuntu recommended
- Connect via VS code
- Install the following VS Code plugin on the remote:
    - Docker

## Install Docker

Depending on what <u>OS</u> you are running, installation is different, but head over to the Community Edition website and follow the instructions there.

- [Ubuntu](https://docs.docker.com/engine/install/ubuntu/)
- [Centos](https://docs.docker.com/engine/install/centos/)
- [Oracle Linux 8](https://oracle-base.com/articles/linux/docker-install-docker-on-oracle-linux-ol8)


> ⚠️ Make sure you execute [Linux post-installation steps](https://docs.docker.com/engine/install/linux-postinstall/)


Try running a command with Docker:
```shell
docker container run hello-world 
```
Your terminal output should look like this:
```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
78445dd45222: Pull complete
Digest:
sha256:c5515758d4c5e1e838e9cd307f6c6a0d620b5e07e6f927b07d05f6d12a1ac8d7
Status: Downloaded newer image for hello-world:latest
Hello from Docker!
This message shows that your installation appears to be working correctly.
To generate this message, Docker took the following steps:
1. The Docker client contacted the Docker daemon.
2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
3. The Docker daemon created a new container from that image which runs
the executable that produces the output you are currently reading.
4. The Docker daemon streamed that output to the Docker client, which
sent it to your terminal.
To try something more ambitious, you can run an Ubuntu container with:
$ docker container run -it ubuntu bash
Share images, automate workflows, and more with a free Docker ID:
https://cloud.docker.com/
For more examples and ideas, visit:
https://docs.docker.com/engine/userguide/
This message shows that your installation appears to be working correctly. Q: So, what did this do?

Try to run docker container run hello-world again.
Docker has now already downloaded the image locally, and can therefore execute the container straight away.
```

# Task 2: Running your first container from image
Now that you have everything setup, it's time to get your hands dirty. In this section, you are going to run an Alpine Linux container (a lightweight linux distribution) on your system and get a taste of the docker container run command.

To get started, let's run the following in our terminal:
```shell
docker image pull alpine
```
Note: Depending on how you've installed docker on your system, you might see a *permission denied error* after running the above command. You may need to prefix your docker commands with sudo as stated before. Alternatively you can create a docker group to get rid of this issue.

This is most likely caused by missing the post installation steps from the Task 1. You can try [Linux post-installation steps](https://docs.docker.com/engine/install/linux-postinstall/).

The `pull` command fetches the alpine image from the Docker registry and saves it in your system. You can use the `docker image ls` command to see a list of all images on your system.
```shell
docker image ls
```
```
REPOSITORY TAG IMAGE ID CREATED VIRTUAL SIZE
alpine latest c51f86c28340 4 weeks ago 1.109 MB
hello-world latest 690ed74de00f 5 months ago 960 B
```
# Task 3: Docker container run
Great! Now let's run a Docker container based on this image. To do that you are going to use the docker container run command.
```shell
docker container run alpine ls -l
```
```
total 48
drwxr-xr-x 2 root root 4096 Mar 2 16:20 bin
drwxr-xr-x 5 root root 360 Mar 18 09:47 dev
drwxr-xr-x 13 root root 4096 Mar 18 09:47 etc
drwxr-xr-x 2 root root 4096 Mar 2 16:20 home
drwxr-xr-x 5 root root 4096 Mar 2 16:20 lib
```
What happened? Behind the scenes, a lot of stuff happened. When you call run,
- The Docker client contacts the Docker daemon
- The Docker daemon creates the container and then runs a command in that container.
- The Docker daemon streams the output of the command to the Docker client

When you run `docker container run alpine`, you provided a command (`ls -l`), so Docker started the command specified and you saw the listing. Let's try something more exciting:
```shell
docker container run alpine echo "hello from alpine"
```
```
hello from alpine
```
OK, that's some actual output. In this case, the Docker client dutifully ran the echo command in our alpine container and then exited it. If you've noticed, all of that happened pretty quickly. Imagine booting up a virtual machine, running a command and then killing it. Now you know why they say containers are fast!

Try another command:
```shell
docker container run alpine /bin/sh
```
Wait, nothing happened! Is that a bug? Well, no. These interactive shells will exit after running any scripted commands, unless they are run in an interactive terminal - so for this example to not exit, you need to:
```shell
docker container run -it alpine /bin/sh
```
You are now inside the container shell and you can try out a few commands like `ls -l`, `uname -a` and others. Exit out of the container by giving the `exit` command.

Ok, now it's time to see the docker container ls command. The docker container ls command shows you all containers that are currently running.
```shell
docker container ls
```
Is this a bug? Also, no; when you wrote exit in the shell, the process stopped. No containers are running, you see a blank line. Let's try a more useful variant: `docker container ls -a`

What you see above is a list of all containers that you ran. Notice that the STATUS column shows that these containers exited a few minutes ago.

Try using the _run_ command again with the flags `-it` (see note below), so it attaches you to an interactive tty in the container. You can run as many commands in the container as you want! Take some time to run your favorite commands. Remember, you can write exit when you want to quit.

Note: the flags `-it` are short for `-i -t` which again are the short forms of **interactive** (Keep STDIN open) and **tty** (Allocate a terminal)

# Task 4: Naming your container
Take a look again at the output of the docker container ls -a:
All containers have an **ID** and a **name**. Both the ID and name is generated every time a new container spins up with a random seed for uniqueness. If you want to assign a specific name to a container then you can use the name option. That can make it easier for you to reference the container going forward.
```shell
docker container run --name alpine01 -it alpine /bin/sh 
```
To find out more about run, use docker container run help to see a list of all flags it supports.

# Task 5: Throw your container away
As containers are just a thin base layer on top of the host kernel, it is really fast to spin up a new instance if you crashed your old one.

Let's try to run an alpine container and delete the file system.

Spin up the container with `docker container run -ti` alpine and list all the folders on the root level to see the whole distribution:
```shell
ls /
```
```
bin etc lib mnt root sbin sys usr
dev home media proc run srv tmp var
```
**Warning:** Do never try to run the following command as a super user in your own environment, as it will delete **everything** on your computer.
Now, delete the whole file system with `rm -rf /`
Try to navigate around to see how much of the OS is gone
```shell
ls
```
```
/bin/sh: ls: not found
```

```shell
whoami
```
```
sh: whoami: not found
```
```shell
date
```
```
/bin/sh: date: not found
```
Exit out by _Ctrl+D_ and create a new instance of the Alpine image and look a bit around:
```shell
docker container run -ti alpine
```
```shell
ls /
```
```
bin etc lib mnt root sbin sys usr
dev home media proc run srv tmp var
```
Try to perform the same tasks as displayed above to see that you have a fresh new instance ready to go.

# Task 6: Auto-remove a container after use
Every time you create a new container, it will take up some space, even though it usually is minimal. To see what your containers are taking up of space try to run the `docker container ls -as` command.
```
CONTAINER ID   IMAGE     COMMAND     CREATED          STATUS                       PORTS     NAMES             SIZE
e61c2ef2dd19   alpine    "/bin/sh"   44 seconds ago   Exited (130) 6 seconds ago             wizardly_bartik   0B (virtual 5.57MB)
```
Here you can see that the alpine image itself takes 5.57MB, and the container itself takes 0B. When you begin to manipulate files in your container, the size of the container will rise.

If you are creating a lot of new containers eg. to test something, you can tell the Docker daemon to remove the container once stopped with the `rm` option: `docker container run rm -it alpine` This will remove the container immediately after it is stopped.

# Task 7: Cleaning up containers you do not use anymore
Containers are still persisted, even though they are stopped. If you want to delete them from your server you need to use the `docker container rm` command. `docker container rm` can take either the CONTAINER ID or NAME as seen above. Try to remove the hello-world container:
```shell
docker container ls -a
```
```
CONTAINER ID   IMAGE         COMMAND     CREATED          STATUS                       PORTS     NAMES
33b8bef3e7f2   hello-world   "/hello"    17 seconds ago   Exited (0) 15 seconds ago              agitated_edison
e61c2ef2dd19   alpine        "/bin/sh"   4 minutes ago    Exited (130) 3 minutes ago             wizardly_bartik
```
```shell
docker container rm agitated_edison agitated_edison
```
The container is now gone when you execute a `ls -a` command.

Tip: As with Git, you can use any unique part of the container ID to refer to it.

# Task 8: Deleting images
You deleted the container instance above, but not the image of hello-world itself. And as you are now on the verge to become a docker expert, you do not need the hello-world image anymore so let us delete it.

First off, list all the images you have downloaded to your computer:
```shell
docker image ls
```
```
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
alpine        latest    0ac33e5f5afa   6 weeks ago    5.57MB
hello-world   latest    feb5d9fea6a5   8 months ago   13.3kB
```
Here you can see the images downloaded as well as their size. To remove the hello-world image use the `docker image rm` command together with the id of the docker image:
```shell
docker image rm feb5
```
```
Untagged: hello-world:latest
Untagged: hello-world@sha256:80f31da1ac7b312ba29d65080fddf797dd76acfb870e677f390d5acba9741b17
Deleted: sha256:feb5d9fea6a5e9606aa995e879d862b825965ba48de054caab5ef356dc6b3412
Deleted: sha256:e07ee1baac5fae6a26f30cabfe54a36d3402f96afda318fe0a96cec4ca393359
```
What docker did here was to _untag_ the image removing the references to the _sha_ of the image. After the image has no references, it deletes the two layers the image itself is comprised of.

# Task 9: Cleaning up
When building, running and rebuilding images, you download and store a lot of layers. These layers will not be deleted, as docker takes a very conservative approach to clean up.

Docker provides a prune command, taking all dangling containers/images/networks/volumes.

```shell
docker container prune
docker image prune
docker network prune
docker volume prune
```
The docker image prune command allows you to clean up unused images. By default, docker image prune only cleans up dangling images. A dangling image is one that is not tagged and is not referenced by any container. To remove all unused resources, resources that are not directly used by any existing containers, use the -a switch as well.

If you want a general clean-up, then `docker system prune` is your friend.

You have seen the swiftness of creating a new container from an image, trash it, and create a new one on top of it. You have learned to use container rm for deleting containers, image rm for images, image ls for listing the images and container ls -a to look at all the containers on your host.

# Task 10: Create a basic webserver
Running arbitrary Linux commands inside a Docker container is fun, but let's do something more useful.

Pull down the nginx Docker image from the Docker Hub. This Docker image uses the Nginx webserver to serve a static HTML website.

Start a new container from the nginx image that exposes port 80 from the container to port 8080 on your host. You will need to use the -p flag with the docker container run command.

> 💡 Mapping ports between your host machine and your containers can get confusing. Here is the syntax you will use:
```shell
docker container run -p 8080:80 nginx 
```
The trick is to remember that the host port always goes to the left, and the container port always goes to the right. Remember it as traffic coming from the host, to the container.

If port **8080** is not port-forwarded on the Host replace it with one which is forwarded, in order to access it from your browser.

If you see a webpage saying `Welcome to nginx!` then you're done!

If you look at the console output from docker, you see nginx producing a line of text for each time a browser hits the webpage:
```shell
docker container run -p 8080:80 nginx 
```
Press control + c in your terminal window to stop your container.

# Task 11: Working with your docker container
When running a webserver like nginx, it's pretty useful that you do not have to have an open session into the server at all times to run it.

We need to make it run in the background, freeing up our terminal for other things. Docker enables this with the -d parameter for run.
```shell
docker container run -p 8080:80 -d nginx
```
```
78c943461b49584ebdf841f36d113567540ae460387bbd7b2f885343e7ad7554
```
Docker prints out the container ID and returns to the terminal.

# Task 12: Attaching to your container
If you want to go into a container again to execute something you have two options:
- attach Attach to the specified process running on the container. In our example it is the nginx server.
- exec Executing another process inside the container. This could be a shell, or a script of some sort.

NOTE: When you attach to an already started container, you cannot exit normally without killing the container unless you issue the attach command like this: docker container attach `--sig-proxy=false` CONTAINER or by issuing _Ctrl+p_ _Ctrl+q_ If you issue the exec command, you can stop it by _Ctrl+d_ or detach yourself by _Ctrl+p_ _Ctrl+q_

First, start up an Nginx container:
```shell
docker container run -d -p 8000:80 nginx
```
```
661c6dd59d78b97f8142d67eff6b1d58fbbd42247900241e08f46abdbad19f06
```
Try to attach to the container. Exit it, and browse the webpage again to acknowledge it is gone.

Step into a new container by executing a bash inside the container:
```shell
docker container run -d -p 8000:80 nginx
docker container exec -it CONTAINER bash
```
Inside, we want to run a longer running process, like pinging itself 100 times. Because containers only have the bare minimum installed, we need to first install ping, and then use it:
```shell
apt-get update && apt-get install iputils-ping -y
ping 127.0.0.1 -c 100 > /tmp/ping
```
Then detach from the container with Ctrl+p Ctrl+q and run the following:
```shell
docker container exec -it CONTAINER bash
tail -f /tmp/ping
```
```
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.156 ms
64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.183 ms
64 bytes from 127.0.0.1: icmp_seq=4 ttl=64 time=0.158 ms
64 bytes from 127.0.0.1: icmp_seq=5 ttl=64 time=0.139 ms
```
Here you see that the ping process started in another shell is still running and producing this logfile. Just stop the process with a _Ctrl+d_.

# Task 13: Docker volumes
Not everything can be in a container. The whole idea is that you can start, stop and delete the containers without losing data. So, if you need to persist data, do it outside of the containers.

You have two different ways of mounting data from your container **bind mounts** and **volumes**.

**A bind mount** is the simpler one to understand. It takes a host path /data and mounts it inside your container eg. /opt/app/data. The positive about bind is that it is easy and connects directly to the host filesystem. The downside is that you need to specify it at runtime with no indication on what mounts a given container has, and that you need to deal with backup, migration etc. in a tool outside the Docker ecosystem.

**A docker Volume** is where you can use a named or unnamed volume to store the external data. You would normally use a volume driver for this, but you can get a host mounted path using the default local volume driver.

## Task 13.1: Bind mounts

So, let's look at the Nginx container. The server itself is of little use, if it cannot access our web content on the host.
We need to create a mapping between the host system, and the container with the `-v` command:
```shell
docker container run --name some-nginx -v /some/content:/usr/share/nginx/html:ro -d nginx
```
That will map whatever files are in the `/some/content` folder on the host to `/usr/share/nginx/html` in the container.

The `:ro` attribute is making the host volume read-only, making sure the container cannot edit the files on the host.
Try to do the following:

Try to create a file in `/some/content`. You can use the command pwd (Print working directory) to display where you are.

This will give you a nginx server running, serving your _static files_ But on which port?

Run a docker container ls command to find out if it has any ports forwarded from the host.
TIPS: Make it host the site on port 8000 Check that it is running by navigating to the hostname or IP with your browser, and on port 8000.

## Task 13.2: Volumes

Volumes are entities inside docker, and can be created in three different ways.
- By explicitly creating it with the docker volume create \<volume_name> command
- By creating a named volume at container creation time with docker container run -d -v DataVolume:/opt/app/data nginx
- By creating an anonymous volume at container creation time with docker container run -d -v /opt/app/data nginx
First off, let's try to make a data volume called data:
```shell
docker volume create data 
```
Docker creates the volume and outputs the name of the volume created.

If you run `docker volume ls` you will have a list of all the volumes created and their driver:
```shell
docker volume ls
```
```
DRIVER VOLUME NAME
local data
```
Unlike the bind mount, you do not specify where the data is stored on the host.

In the volume API, like for almost all other of Docker's APIs, there is an inspect command giving you low level details. Let's use it against the html volume.
```shell
docker volume inspect data
```
```json
[
    {
        "CreatedAt": "2022-05-23T04:58:10-04:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/data/_data",
        "Name": "data",
        "Options": {},
        "Scope": "local"
    }
]
```
You can see that the data volume is mounted at `/var/lib/docker/volumes/data/_data` on the host.

Note we will not go through the different drivers. For more info look at Dockers own example.

You can now use this data volume in all containers. Try to mount it to an nginx server with the command:
```shell
docker container run --rm --name www -d -p 8080:80 -v data:/usr/share/nginx/html nginx
```
**Note:** If the volume refers to is empty and we provide the path to a directory that contains data in the base image, that data will be copied into the volume.

Try now to look at the data stored in /var/lib/docker/volumes/data/_data on the host:
```shell
sudo ls /var/lib/docker/volumes/data/_data/
```
```
50x.html index.html
 ```
Those two files come from the Nginx image and is the standard files the webserver has.

# Task 14: Attaching multiple containers to a volume
Multiple containers can attach to the same volume with data. Docker doesn't handle any file locking, so applications must account for the file locking themselves.

Let's try to go in and make a new html page for nginx to serve. We do this by making a new ubuntu container that has the data volume attached to /tmp, and thereafter create a new html file with the echo command:
```
docker container run -ti --rm -v data:/tmp ubuntu bash
root@9c36fcfcc048:# echo "<html><h1>hello world</h1></html>" > /tmp/hello.html
root@9c36fcfcc048:# ls /tmp
hello.html 50x.html index.html
```
Head over to your newly created webpage using curl localhost:8080/hello.html

# Task 15: Cleanup
Exit out of your ubuntu server _(the container you have last created)_ and execute a `docker container stop www` to stop the nginx container.

Run a docker container ls to make sure that no other containers are running.
```shell
docker container ls 
```
The data volume is still present, and will be there until you remove it with a `docker volume rm data` or make a general clean-up of all the unused volumes by running `docker volume prune`.

**Tips and tricks** As you have seen, the `-v` flag can both create a bind mount or name a volume depending on the syntax. If the first argument begins with a `/` or `~/` you're creating a bind mount. Remove that, and you're naming the volume. For example:
- `-v /path:/path/in/container` mounts the host directory, /path at the /path/in/container
- `-v path:/path/in/container` creates a volume named path with no relationship to the host.

**Sharing data** If you want to share volumes or bind mounts between two containers, then use the `volumes-from` option for the second container. The parameter maps the mapped volumes from the source container to the container being launched.

# Task 16: More advanced docker commands
Before you go on, use the Docker command line interface documentation to try a few more commands:
- While your detached container is running, use the docker container ls command to see what silly name Docker gave your container. This is one command you're going to use often!
- While your detached container is still running, look at its logs. Try following its logs and refreshing your browser.
- Stop your detached container, and confirm that it is stopped with the ls command.
- Start it again, wait 10 seconds for it to fire up, and stop it again.
- Then delete that container from your system.

**NOTE:** When running most docker commands, you only need to specify the first few characters of a container's ID. For example, if a container has the `ID df4fd19392ba`, you can stop it with `docker container stop df4`. You can also use the silly names Docker provides containers by default, such as _boring_bardeen_.

# Task 17: Constructing a docker image
Running containers others made is useful, but if you want to use docker for production, chances are you want to construct a container on your own.

## Task 17.1: Dockerfile commands summary

Here's a quick summary of some basic commands we will use in our Dockerfile.
As a rule of thumb, all commands in CAPITAL LETTERS are intended for the docker engine.
- FROM
- RUN
- ADD and COPY
- CMD
- EXPOSE
- ENTRYPOINT

Details:

**FROM** is always the first item in the Dockerfile. It is a requirement that the Dockerfile starts with the _FROM_ command. Images are created in layers, which means you can use another image as the base image for your own. The _FROM_ command defines your base layer. As argument, it takes the name of the image. Optionally, you can add the Docker Hub username of the maintainer and image version, in the format username/imagename:version.

**RUN** is used to build up the image you're creating. For each RUN command, Docker will run the command then create a new layer of the image. This way you can roll back your image to previous states easily. The syntax for a RUN instruction is to place the full text of the shell command after the RUN (e.g., `RUN mkdir /user/local/foo`). This will automatically run in a `/bin/sh` shell. You can define a different shell like this: `RUN /bin/bash -c 'mkdir /user/local/foo'`

**COPY** copies local files into the container. The files need to be in the same folder (or a sub folder) as the Dockerfile itself. An example is copying the requirements for a python app into the container: `COPY requirements.txt /usr/src/app/.`

**ADD** should only be used if you want to copy and unpack a tar file into the image. In any other case, use _COPY_. _ADD_ can also be used to add a file directly from an _URL_; consider whether this is good practice.

**CMD** defines the commands that will run on the image at start-up. Unlike a RUN, this does not create a new layer for the image, but simply runs the command. There can only be one CMD in a Dockerfile. If you need to run multiple commands, the best way to do that is to have the CMD run a script. CMD requires that you tell it where to run the command, unlike RUN. So example CMD commands would be:
```dockerfile
CMD ["python", "./app.py"]
CMD ["/bin/bash", "echo", "Hello World"]
```

**EXPOSE** creates a hint for users of an image that provides services on ports. It is included in the information which can be retrieved via `docker container inspect <container-id>`.

Note: The EXPOSE command does not actually make any ports accessible to the host! Instead, this requires publishing ports by means of the `-p` or `-P` flag when using `docker container run`.

**ENTRYPOINT** configures a command that will run no matter what the user specifies at runtime.

## Task 17.2: Write a Dockerfile

We want to create a Docker image with a Python web app.

As mentioned above, all user images are based on a base image. Since our application is written in Python, we will build our own Python image based on Ubuntu. We'll do that using a Dockerfile.

**Note: **If you want to learn more about Dockerfiles, check out Best practices for writing Dockerfiles. A Dockerfile is a text file containing a list of commands that the Docker daemon calls while creating an image. The Dockerfile contains all the information that Docker needs to know to run the app; a base Docker image to run from, location of your project code, any dependencies it has, and what commands to run at start-up. It is a simple way to automate the image creation process. The best part is that the commands you write in a Dockerfile are almost identical to their equivalent Linux commands. This means you don't really have to learn new syntax to create your own Dockerfiles.

Create a file called `Dockerfile`, and add content to it as described below.

We'll start by specifying our base image, using the FROM keyword:
```dockerfile
FROM ubuntu:latest
```
The next step is usually to write the commands of copying the files and installing the dependencies. But first we will install the Python pip package to the ubuntu linux distribution. This will not just install the pip package but any other dependencies too, which includes the python interpreter. Add the following RUN command next:
```dockerfile
RUN apt-get update -y
RUN apt-get install -y python3-pip python3-dev build-essential
```
Let's add the files that make up the Flask Application.
Install all Python requirements for our app to run. This will be accomplished by adding the lines:
```dockerfile
COPY requirements.txt /usr/src/app/
RUN pip install --no-cache-dir -r /usr/src/app/requirements.txt
```
Copy the application app.py into our image by using the COPY command.
```dockerfile
COPY app.py /usr/src/app/
```
Specify the port number which needs to be exposed. Since our flask app is running on 5000 that's what we'll expose.
```dockerfile
EXPOSE 5000
```
The EXPOSE instruction does not actually publish the port. It functions as a type of documentation between the person who builds the image and the person who runs the container, about which ports are intended to be published. You need the -p/-P command to actually open the host ports.
The last step is the command for running the application which is simply - python ./app.py. Use the CMD command to do that:
```dockerfile
CMD ["python3", "/usr/src/app/app.py"] 
```
The primary purpose of CMD is to tell the container which command it should run by default when it is started. Verify your Dockerfile.
Our Dockerfile is now ready. This is how it looks:
```dockerfile
# The base image
FROM ubuntu:latest
# Install python and pip
RUN apt-get update -y
RUN apt-get install -y python3-pip python3-dev build-essential
# Install Python modules needed by the Python app
COPY requirements.txt /usr/src/app/
RUN pip install --no-cache-dir -r /usr/src/app/requirements.txt
# Copy files required for the app to run
COPY app.py /usr/src/app/
# Declare the port number the container should expose
EXPOSE 5000
# Run the application
CMD ["python3", "/usr/src/app/app.py"]
```
The file requirements.txt should contain:
```
Flask==2.0.3 
```
The app.py file should contain:
```python
cat app.py
import socket
from flask import Flask
app = Flask(__name__)
@app.route('/')
def hello_world():
    return "Hello! I am a Flask application running on
{}".format(socket.gethostname())
 
if __name__ == '__main__':
# Note the extra host argument. If we didn't have it, our Flask app
# would only respond to requests from inside our container
app.run(host='0.0.0.0')
```
Now that you have your _Dockerfile_, you can build your image. The docker build command does the heavy-lifting of creating a docker image from a _Dockerfile_.
The `docker build` command is quite simple - it takes an optional tag name with the `-t` flag, and the location of the directory containing the _Dockerfile_ - the `.` indicates the current directory:
```shell
docker build -t myfirstapp .
```
```
Sending build context to Docker daemon 5.12kB
Step 1/8 : FROM ubuntu:latest
latest: Pulling from library/ubuntu
b6f892c0043b: Pull complete
55010f332b04: Pull complete
2955fb827c94: Pull complete
3deef3fcbd30: Pull complete
cf9722e506aa: Pull complete
Digest:
sha256:382452f82a8bbd34443b2c727650af46aced0f94a44463c62a9848133ecb1aa8
Status: Downloaded newer image for ubuntu:latest
---> ebcd9d4fca80
Step 2/8 : RUN apt-get update -y
---> Running in 42d5752a0faf
Get:1 http://archive.ubuntu.com/ubuntu xenial InRelease [247 kB]
Get:2 http://security.ubuntu.com/ubuntu xenial-security InRelease [102
kB]
...
```
If you don't have the ubuntu:latest image, the client will first pull the image and then create your image. If you do have it, your output on running the command will look different from mine. If everything went well, your image should be ready! Run docker image ls and see if your image (myfirstapp) shows.

# Task 17.3: Run your image

The next step in this section is to run the image and see if it actually works:
```shell
docker container run -p 8888:5000 --name myfirstapp myfirstapp
```
```
* Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
Head over to localhost:8888 or your server's URL and your app should be live.
```

# Task 18: Images and layers
When dealing with docker images, a layer, or image layer is a change on an image, or an intermediate image. Every time you run one of the commands RUN, COPY or ADD in your Dockerfile it causes the previous image to change, thus creating a new layer.

Consider the following Dockerfile:
```dockerfile
FROM ubuntu:18.04
RUN apt-get update -y
RUN apt-get install -y python-pip python-dev build-essential
COPY requirements.txt /usr/src/app/
RUN pip install --no-cache-dir -r /usr/src/app/requirements.txt
COPY app.py /usr/src/app/
EXPOSE 5000
CMD ["python", "/usr/src/app/app.py"]
```
First, we choose a starting image ubuntu:latest, which in turn has many layers. We add another layer on top of our starting image, running an update on the system. After that yet another for installing the python ecosystem. Then, we tell docker to copy the requirements to the container. That's another layer. The concept of layers comes in handy at the time of building images. Because layers are intermediate images, if you make a change to your Dockerfile, docker will build only the layer that was changed and the ones after that. This is called layer caching.

Each layer is built on top of its parent layer, meaning if the parent layer changes, the next layer does as well. If you want to concatenate two layers (e.g. the update and install which is a good idea), then do them in the same `RUN` command:
```dockerfile
FROM ubuntu:18.04
RUN apt-get update && apt-get install -y \
python-pip \
python-dev \
build-essential
COPY requirements.txt /usr/src/app/
RUN pip install --no-cache-dir -r /usr/src/app/requirements.txt
COPY app.py /usr/src/app/
EXPOSE 5000
CMD ["python", "/usr/src/app/app.py"]
```
If you want to be able to use any cached layers from last time, they need to be run before the update command.

The file `requirements.txt` should contain:
```
Flask==1.0 
```

**NOTE:** Once we build the layers, Docker will reuse them for new builds. This makes the builds much faster. This is great for continuous integration, where we want to build an image at the end of each successful build (e.g. in Jenkins). But the build is not only faster, the new image layers are also smaller, since intermediate images are shared between images.

Try to move the two COPY commands before for the RUN and build again to see it taking the cached layers instead of making new ones.

# Task 19: Every layer can be a container
As stated above, all FROM, RUN, ADD, COPY, CMD and EXPOSE will create a new layer in your image, and therefore also be an image of their own.

Take a look again at some of the output from building the image above:
```
---> c1f2dc732c7c
Removing intermediate container f92f9c719287
Step 6/8 : COPY app.py /usr/src/app/
---> 6ed47d3c544a
Removing intermediate container 61a68a949d68
Step 7/8 : EXPOSE 5000
---> Running in 1f939928b7d5
---> 6c14a93b72f2
```
So, what docker actually does is
- Taking the layer created just before
- make a container based of it
- run the command given
- save the layer.

in a loop untill all the commands have been made. Try to create a container from your `COPY app.py /usr/src/app/` command. The id of the layer will likely be different than the example above.
```shell
docker container run -ti -p 5000:5000 6ed47d3c544a bash.
```
You are now in a container run from that layer in the build script. You can't make the EXPOSE command, but you can look around, and run the last python app:
```
root@cc5490748b2a:/# ls
bin boot dev etc home lib lib64 media mnt opt proc root run
sbin srv sys tmp usr var
root@cc5490748b2a:/# ls /usr/src/app/
app.py requirements.txt
root@cc5490748b2a:/# python /usr/src/app/app.py
* Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

And just like the image you built above, you can browse the website now.

# Task 20: Sharing images
Before we can take our dockerized Flask app to another computer, we need to push it up to the Docker Hub so that we (or other people) can pull it down on other machines.

Docker Hub is sort of like GitHub of Docker images. It's the main place people store their Docker images (publicly) in the cloud.

First, create an account on the Docker Hub if you haven't already - it's free: https://hub.docker.com/signup Then, login to that account by running the docker login command on your laptop.

We're almost ready to push our Flask image up to the Docker Hub. We just need to rename it to our namespace first.

Using the docker tag command, tag the image you created in the previous section to your namespace. For example, I would run:
```shell
docker tag myfirstapp johndoeofdocker/myfirstapp:latest
```
myfirstapp is the tag I used in my docker build commands in the previous section, and johndoeofdocker/myfirstapp: is the full name of the new Docker image I want to push to the Hub.

johndoeofdocker is my username at dockerhub, and also my namespace for all my images. The :latest is a versioning scheme you can append to.

All that's left to do is push up your image:
```shell
docker push johndoeofdocker/myfirstapp
```
The push refers to a repository [docker.io/ johndoeofdocker/myfirstapp]
```
6daf7f1140cb: Pushed
7f74a217d86b: Pushed
09ccfff62b13: Pushed
f83ccb38761b: Pushed
584a7965008a: Pushed
33f1a94ed7fc: Mounted from library/ubuntu
b27287a6dbce: Mounted from library/ubuntu
47c2386f248c: Mounted from library/ubuntu
2be95f0d8a0c: Mounted from library/ubuntu
2df9b8def18a: Mounted from library/ubuntu
latest: digest:
sha256:e7016870c297b3c49996ee00972d8abe7f20b4cbe45089dc914193fa894991d3
size: 2407
```
Go to your profile page on the Docker Hub and you should see your new repository listed: https://hub.docker.com/repos/u/

Congrats! You just made your first Docker image and shared it with the world!
Try to pull down the images that your fellows have pushed to see that it's really up there.

For more info on the Docker Hub, and the cli integration, head over to https://docs.docker.com/docker-hub/ and read the guides there.

# Task 21: Delete your image
If you run a `docker container ls -a` command, you can now see a container with the name `myfirstapp` from the image named `myfirstapp`:
```shell
docker container ls -a 
```
Run a docker image ls command to see that you have a docker image with the name `myfirstapp` Try now to first:
- remove the container `docker rmi <image_name>`
- remove the image file as well with the `docker image rm` command.
- make `docker image ls` again to see that it's gone.

Here is the list of all the instructions that can be used in a Dockerfile:
- .dockerignore
- FROM Sets the Base Image for subsequent instructions.
- RUN execute any commands in a new layer on top of the current image and - commit the results.
- CMD provide defaults for an executing container.
- EXPOSE informs Docker that the container listens on the specified network - ports at runtime. NOTE: does not actually make ports accessible.
- ENV sets environment variable.
- ADD copies new files, directories or remote file to container. Invalidates - caches. Avoid ADD and use COPY instead.
- COPY copies new files or directories to container. Note that this only copies - as root, so you have to chown manually regardless of your USER / WORKDIR - setting. See https://github.com/moby/moby/issues/301109
- ENTRYPOINT configures a container that will run as an executable.
- VOLUME creates a mount point for externally mounted volumes or other - containers.
- USER sets the user name for following RUN / CMD / ENTRYPOINT commands.
- WORKDIR sets the working directory.
- ARG defines a build-time variable.
- ONBUILD adds a trigger instruction when the image is used as the base for - another build.
- STOPSIGNAL sets the system call signal that will be sent to the container to - exit.
- LABEL apply key/value metadata to your images, containers, or daemons.
- HEALTHCHECK adds a healtch check that is run periodically, and depending on the restart policy, it's used by the docker daemon to check if the container should be restarted.

# Task 22: Host a WordPress site
In this scenario, we are going to deploy the CMS system called WordPress.
WordPress is a free and open source blogging tool and a content management system (CMS) based on PHP and MySQL, which runs on a web hosting service.

So, we need two containers:
- One container that can serve the Wordpress PHP files
- One container that can serve as a MySQL database for Wordpress.

Both containers already exists on the dockerhub: Wordpress and MySQL.

To start a MySQL container, issue the following command:
```shell
docker container run --name mysql-container --rm -p 3306:3306 -e MYSQL_ROOT_PASSWORD=wordpress -e MYSQL_DATABASE=wordpress_database -e MYSQL_USER=wordpress_user -e MYSQL_PASSWORD=password -d mysql:5.7
```
Let's recap what this command does:
docker invokes the docker engine
1. container manage the container part of docker
2. run tells docker to run a new container off an image
3. name mysql-container gives the new container a name for better referencing
4. rm tells docker to remove the container after it is stopped
5. -p 3306:3306 mounts the host port 3306, to the containers port 3306.
6. -e MYSQL_* the -e option is used to inject environment variables into the container.
7. -d runs the container detached, in the background.
8. mysql tells what container to actually run, here mysql:latest (:latest is the default if nothing else is specified)

MySQL is now exposing its port 3306 on the host, and everybody can attach to it so **do not do this in production without proper security settings**.

We need to connect our WordPress container to the host's IP address. You can either use the external IP address of your server, or the docker host IP 172.17.0.1.

After you have noted down the IP, spin up the WordPress container with the host IP as a variable:
```shell
docker container run --name wordpress-container --rm -e WORDPRESS_DB_HOST=172.17.0.1 -e WORDPRESS_DB_USER=wordpress_user -e WORDPRESS_DB_PASSWORD=password -e WORDPRESS_DB_NAME=wordpress_database -p 8001:80 -d wordpress
```
You can now browse to the IP:8080 and have your very own WordPress server running. Since port 3306 is the default MySQL port, WordPress will try to connect on that port by itself.

# Task 23: Making a container network
Even though we in two commands made the setup running in the above scenario, there are some problems here we can fix:
- We need to know the host IP to get them to talk to each other.
- And we have exposed the database to the outside world.

In order to connect multiple docker containers without binding them to the hosts network interface we need to create a docker network.

The docker network command securely connect and provide a channel to transfer information from one container to another.

First off make a new network for the containers to communicate through:
```shell
docker network create if_wordpress 
```
Docker will return the networkID for the newly created network. You can reference it by name as well as the ID.

Now you need to connect the two containers to the network, by adding the network option:
```shell
docker container run --name mysql-container --rm --network if_wordpress -e MYSQL_ROOT_PASSWORD=wordpress -e MYSQL_DATABASE=wordpress_database -e MYSQL_USER=wordpress_user -e MYSQL_PASSWORD=password -d mysql:5.7
af38acac52301a7c9689d708e6c3255704cdffb1972bcc245d67b02840983a50
```
```shell
docker container run --name wordpress-container --rm --network if_wordpress -e WORDPRESS_DB_HOST=mysql-container -e WORDPRESS_DB_USER=wordpress_user -e WORDPRESS_DB_PASSWORD=password -e WORDPRESS_DB_NAME=wordpress_database -p 8001:80 -d wordpress
fd4fd096c064094d7758cefce41d0f1124e78b86623160466973007cf0af8556
```
Notice the WORDPRESS_DB_HOST env variable. When you make a container join a network, it automatically gets the container name as DNS name as well, making it super easy to make containers discover each other. The DNS name is only visible inside the Docker network, which is also true for the IP address (usually an address starting with 172) that is assigned to them. If you do not expose a port for a container, the container is only visible to Docker.

You have now deployed both containers into the network. Take a deeper look into the container network by issuing:
```shell
docker network inspect if_wordpress
```
```json
[
    {
        "Name": "if_wordpress",
        "Id": "1782d1c6bd02081026869a7b633973c8b71cac48d79d3843761e81047731ed7c",
        "Created": "2023-05-24T07:04:52.596890472-04:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.19.0.0/16",
                    "Gateway": "172.19.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "25f366b1c128d30d8d02607bccbc335ea9eaaa58723d1db29fb5739ef73598a9": {
                "Name": "mysql-container",
                "EndpointID": "306aa62d0d2a73be2a8958a90b8cb39a49c219d8fc056be862c09c341bf03ffb",
                "MacAddress": "02:42:ac:13:00:02",
                "IPv4Address": "172.19.0.2/16",
                "IPv6Address": ""
            },
            "bd888abf58bf11ca3171ce171e66e00ea9a68525f3c2fede01da836cf3296464": {
                "Name": "wordpress-container",
                "EndpointID": "3e3f4f8b96d93533f667c8bd3689b7701f8772502eb24dd4e93ec06f2e07dda1",
                "MacAddress": "02:42:ac:13:00:03",
                "IPv4Address": "172.19.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
```
As, we have linked both the container now WordPress container can be accessed from browser using the address http://localhost:8080 and setup of WordPress can be done easily. MySQL is not accessible from the outside so security is much better than before.

# Task 24: Cleanup
Close both of the containers down by issuing the following command:
```shell
docker container stop wordpress-container mysql-container
```

# Task 25: Using Docker compose
If you have started working with Docker and are building container images for your application services, you most likely have noticed that after a while you may end up writing long docker container run commands.

These commands, while very intuitive, can become cumbersome to write, especially if you are developing a multi-container application and spinning up containers quickly.

Docker Compose is a tool for defining and running your multi-container Docker applications.

Your applications can be defined in a YAML file where all the options that you used in docker container run are defined.

Compose also allows you to manage your application as a single entity rather than dealing with individual containers.

This file defines all of the containers and settings you need to launch your set of clusters. The properties map onto how you use the docker run commands, however, are now stored in source control and shared along with your code.

**Terminology**
- **docker-compose.yml** the YAML file where all your configuration of your docker - containers go.
- **docker-compose** the cli tool that enables you to define and run - multi-container applications with Docker
- **run:** create the services stated in the compose file
- **up:** creates and starts the services stated in the compose file
- **down:** stops and removes containers, networks, images, and volumes
- **restart:** restarts the services
- **logs:** streams the accumulated logs from all the containers in the compose file
- **ps:** same as pure docker container variant; shows you all containers that are - currently running.
- **rm:** removes all the containers from the given compose file.
- **start:** starts the services
- **stop:** stops the services

The docker cli is used when managing individual containers on a docker engine. It is the client command line to access the docker daemon api.

The docker-compose cli together with the yaml files can be used to manage a multi-container application.

# Task 26: Compose-rising your WordPress
So, we want to take advantage of docker-compose to run our WordPress site. In order to this we need to:
- Transform our setup into a docker-compose.yaml file
- Invoke docker-compose and watch the magic happen!

Create a new docker-compose.yaml file with the following content:

**docker-compose.yaml**
```yaml
version: '3.3'
services:
# wordpress_container:
    mysql_container:
        image: mysql:5.7
        ports:
            - 3306:3306
        environment:
            MYSQL_ROOT_PASSWORD: wordpress
            MYSQL_DATABASE: wordpress_database
            MYSQL_USER: wordpress_user
            MYSQL_PASSWORD: password
```

This is the template we are building our compose file upon so let's drill this one down:
- version indicate what version of the compose syntax we are using
- services is the section where we put our containers
- wordpress_container is the section where we define our wordpress container
- mysql_container is the ditto of MySQL.
For more information on docker-compose yaml files, head over to the documentation.

The services part is equivalent to our docker container run command. Likewise there is a network and volumes section for those as well corresponding to docker network create and docker volume create. Let's look the mysql_container part together, making you able to create the other container yourself. Look at the original command we made to spin up the container:
```shell
docker container run --name mysql-container --rm -p 3306:3306 -e MYSQL_ROOT_PASSWORD=wordpress -e MYSQL_DATABASE=wordpress_database -e MYSQL_USER=wordpress_user -e MYSQL_PASSWORD=password -d mysql:5.7
```
The command gives out following information: a name, a port mapping, an environment variable and the image we want to run.

Now look at the docker-compose example again:
- mysql_container defines the name of the container
- image:wordpress describes what image the container spins up from.
- ports defines a list of port mappings from host to container
- environment describes the -e variable made before in a yaml list

Instead of keeping sensitive information in the docker-compose.yml file, you can also use an .env file to keep all the environment variables. That way, it's easier to make a development environment and a production environment with the same docker-compose.yml. Try to spin up the container in detached mode:
```shell
docker-compose up -d
```
```
Creating network "student_default" with the default driver
Creating student_mysql_container_1 ... done
```

Looking at the output you can see that it made a docker network named student_default as well as the MySQL container named student_mysql_container_1.

Issue a docker container ls as well as docker network ls to see that both the container and network are listed.

To shut down the container and network, issue a docker-compose down **NOTE:** The command docker-compose down removes the containers and default network.

# Task 27: Creating the WordPress container
You now have all the pieces of information to make the WordPress container. We've copied the run command from before if you can't remember it by heart:
```shell
docker container run --name wordpress-container --rm --network if_wordpress -e
WORDPRESS_DB_HOST=mysql-container -e WORDPRESS_DB_PASSWORD=wordpress -p 8080:80 -d wordpress
```
You must
- uncomment the wordpress_container part of the services section
- map the pieces of information from the docker container run command to the yaml format.
- remove MySQL port mapping to close that from outside reach.
```yaml
version: '3.3'
services:
 wordpress-container:
  image: wordpress
  ports:
   - 8001:80
  environment:
   - WORDPRESS_DB_PASSWORD=password
   - WORDPRESS_DB_HOST=mysql-container
   - WORDPRESS_DB_USER=wordpress_user
   - WORDPRESS_DB_NAME=wordpress_database
  depends_on:
   - mysql-container
 mysql-container:
  image: mysql:5.7
  environment:
   -  MYSQL_ROOT_PASSWORD=wordpress
   -  MYSQL_DATABASE=wordpress_database
   -  MYSQL_USER=wordpress_user
   -  MYSQL_PASSWORD=password
```
When you made that, run docker-compose up -d and access your wordpress site from http://IP:8080

# Task 28: Multi Stage Builds
##  Task 28.1: Create a tiny go-application container
Imagine you have the following go application:
**hello.go**
```python
package main
import "fmt"
func main() {
    fmt.Println("Hello world!")
}
```
You want to containerize it. That's easy! You don't even have to have go installed, because you can just use a base image that has go!

**Dockerfile**
```dockerfile
FROM golang:alpine
WORKDIR /app
ADD . /app
RUN go mod init goapp
RUN cd /app && go build -o goapp
ENTRYPOINT ./goapp
```
Try building the image with `docker build -t mysecondapp .` and run it. You should see `Hello world!` printed to the console:
```shell
docker container run mysecondapp
Hello world!
```

## Task 28.2: Using Multi Stage Builds
Now try docker image ls. Could we make it smaller? We only need the compiler on build-time, since go is a statically compiled language.

See the following Dockerfile, it has two build stages, wherein the latter stage is using the compiled artifacts from the first
```dockerfile
# build stage
FROM golang:alpine AS builder
WORKDIR /src
ADD . /src/
RUN go mod init goapp
RUN cd /src && go build -o goapp
 
# final stage
FROM alpine
WORKDIR /app
COPY --from=builder /src/goapp /app/
ENTRYPOINT ./goapp
```

Try building it with a different tag and running it. It should still print `Hello world!` to your console.
Try inspecting the size with docker image ls.

Compare the size of the two images. The latter image should be much smaller, since it's just containing the go-application using alpine as the base image, and not the entire golang-suite of tools.

You can read more about this on: Use multi-stage builds - docs.docker.com

## Task 28.3: Bonus exercise
Since go is a statically compiled language, we can actually use scratch as the base image.

**Hint:** the `scratch` container has no shell, so in the Dockerfile you also need to change the entrypoint from shell form to exec form. See: ENTRYPOINT under Dockerfile reference.

After building with your new Dockerfile, inspect the size of the images. Your new image should be even smaller than the alpine example.

# Task 29: dockerignore
Before the docker CLI sends the context to the docker daemon, it looks for a file named .dockerignore in the root directory of the context. If this file exists, the CLI modifies the context to exclude files and directories that match patterns in it. This helps to avoid unnecessarily sending large or sensitive files and directories to the daemon and potentially adding them to images using ADD or COPY.