# Learning Environment for Ansible using Docker
This repo contains the source code for dockerfiles and docker compose files for creating an environment for ansible and for it to work properly


## Why?
The one and only reason of using docker containers in this project is memory effiency and low-hardware friendliness.

In normal circumstances, if you were to run 5 machines (may it be VM or Physical). It would cost you more money, RAM and Hardware. By using these containers one can virtually do anything which he/she could have done in those 5 machines using Ansible and by only spending a total amount of 70 MBs of RAM!

This means you can easily run the whole setup on a normal 4 GB RAM Laptop/PC.

That's the actual idea behind creating this project.


## Running the docker containers using a simple command!

If you have not installed `docker` in your machine then [install `docker`](INSTALL_DOCKER.md) first then proceed to the next step.

Run this in your terminal in the same directory as this project:
```bash
git clone --depth 1 https://github.com/hussain1406/learn-ansible-using-docker && cd learn-ansible-using-docker
cd "docker_compose" && docker compose up -d
```
and done! Docker will automatically pull the images and create "**6 containers**" (🚀🚀).

Here is a breakdown of all the containers created:
- First container will be the **master container** based on ansible installed in alpine linux.
- Next 3 will be Ubuntu version 18.04, 20.04, 22.04 respectively
- Last 2 will be RockyLinux (Open-Source RHEL-based distribution) version 8 and 9 respectively

For more info on how these containers are linked see [compose.yaml](docker_compose/compose.yaml)

Note that password-less authentication is set up in these containers so you don't need to configure anything else

Your hosts file will be mounted (keep in sync) inside the `ansible_master_alpine` (The container in which ansible is installed) container and will be kept in sync alongside the `ansible.cfg` file. So you can edit those files, without going into the ansible master container, directly from your base OS.

To interact with ansible master run:

```bash
docker exec -it ansible_master_alpine ash
```

to test that everything is working correctly (which it should) run this inside the container (if you ran the command before this you are already inside the container):
```bash
ansible all -m ping
```

Now when you are ready to destroy your containers (note that the `/home/ansible/ansible/` directory won't be deleted as it is mounted from the host as a volume) then you can simply run this command:

```bash
docker compose down
docker compose rm -f
```

Now You should remember that the `ansible_config` folder is mapped with the `/home/ansible/ansible/` folder on `ansible_master_alpine`

So any data that is in `/home/ansible/ansible/` inside `ansible_master_alpine` container is saved on your local filesystem in your `ansible_config` directory.


## Well there's more!!
Now the things here are a bit advanced. It requires you to know `docker` and How to create Dockerfiles. And a little bit of Bash Scripting.

Firstly, key things to remember are:

1. The image you will get will be prefixed with `hussain14` username. You can change it in the [build.sh](dockerfiles/build.sh) script by changing the value of the `user_name`. (Don't change it to `""`. Username should be valid user on DockerHub if you want to push to DockerHub)
2. There are two functions for their specific use cases:
   - First one is `remove_image` this take one argument specifically an image name to delete and handles the exceptions in interactive way
   - Second one is `build_image` which takes exactly two arguments. i.e. first one as the name of the image and second one as the path to Dockerfile

Well you can generate your own set of images with tweaks you want by first editing the respective dockerfile and then running the build.sh script from it's own directory such that if you are in the root directory of this repository then you probably should run this:
```bash
cd "dockerfiles" && bash build.sh
```
You can also build your own customized images for ansible and its nodes. You just have to add the docker file for that image containing all the necessary code and then use the function inside [bulid.sh](dockerfiles/build.sh) and the next time you build your images it will build it too.

Happy Learning!
