# Docker Engine

[Docker Engine](https://docs.docker.com/engine/) is an open source containerization technology for building and containerizing your applications. Docker Engine acts as a client-server application with:
- A server with a long-running daemon process dockerd.
- APIs which specify interfaces that programs can use to talk to and instruct the Docker daemon.
- A command line interface (CLI) client docker.

The CLI uses Docker APIsto control or interact with the Docker
daemon through scripting or direct CLI commands. Many other Docker applications
use the underlying API and CLI. The daemon creates and manage Docker objects,
such as images, containers, networks, and volumes.

## Adding the External Repo

Since Docker is not available on RHEL 8 / CentOS 8, we need to add an external repository to obtain the software. In this case we will use the official Docker CE CentOS repository: this is, at the moment of writing, the only way to install Docker CE on RHEL 8 / CentOS 8.

### First Step
We need to review the defailt.repo file to ensure the docker-ce repo is commented out (#). Located in /etc/yum.repos.d/default.repo

### Second Step
The dnf config-manager utility will allow us, among the other things, easily enable or disable a repository in this installation. By default, only the appstream and baseos repositories are enabled on Rhel8; we need to add and enable also the docker-ce repo. All we need to do to accomplish this task, is to run the following command:

```shell
sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
```

### Verify
We can verify that the repository has been enabled, by looking at the output of the following command:

```shell
 $ sudo dnf repolist -v
 ```
The result should be similar to the below:
![Example Output](images/RepoList.png)

## Installing Docker-CE

### List the available packages in the repo

```shell
sudo dnf list docker-ce --showduplicates | sort -r
```
### Install the Latest Docker-CE
Set best option as false, therefore transactions are not limited to only best candidates. Prior to recent updates, only certain versions of Docker still worked on RHEL 8

```shell
sudo dnf install docker-ce --nobest -y
```

The result should be similar to the below:
![Example Output](images/DockerCEOutput.png)

### Verify by checking Docker Version
```shell
docker --version
```

## Docker-CE Daemon
### Start and Enable the Docker Daemon
Once docker-ce is installed, we must start and enable the docker daemon, so that it will be also launched automatically at boot. The command we need to run is the following:
```shell
sudo systemctl enable --now docker
```

At this point, we can confirm that the daemon is active by running:
```shell
sudo systemctl is-active docker
```

Similarly, we can check that it is enabled at boot, by running:
```shell
sudo systemctl is-enabled docker
```

Alternate way tp check the status:
```shell
systemctl status docker
```

## Test Docker-CE Install
We will test the install by running  Hello World.  This will pull the Hello World Container and run it.
```shell
sudo docker run hello-world
```

## Docker Admin Commands
```shell
sudo docker ps
```
```shell
sudo docker ps -a
```
```shell
sudo docker volume ls
```

### Clean Up
To remove all containers and volumes:
```shell
sudo docker container prune
```

```shell
sudo docker volume prune
```
### Cheat Sheet of Docker commands
Additional Commands located here: https://dockerlabs.collabnix.com/docker/cheatsheet/
