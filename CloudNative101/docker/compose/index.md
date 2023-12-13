# Docker Compose

[Compose](https://docs.docker.com/compose/) is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration. Compose works in all environments: production, staging, development, testing, as
well as CI workflows.

## Global Installation
The way we should install docker-compose varies depending on whether we want to install it globally or just for a single user.  We are going to install it Globally, so we need to download the binary from the github page of the project:

### Install Curl
If it is not already loaded, we need to load this.  Curl is used in command lines or scripts to transfer data with URLs. curl is also used in cars, television sets, routers, printers, audio equipment, mobile phones, tablets, media players and is the Internet transfer engine for thousands of software applications in over ten billion installations.
```shell
sudo dnf -y install curl
```

### Download and Install Compose
Download and Install to /user/local/bin:
```shell
sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-
$(uname -m) -o /usr/local/bin/docker-compose
```

Make it Executable:
```shell
sudo chmod +x /usr/local/bin/docker-compose
```

Verify it is Global and Accessible
```shell
docker-compose version
```

Setup a Symbolic Link (provide access to /usr/bin & /usr/local/bin)
```shell
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```
