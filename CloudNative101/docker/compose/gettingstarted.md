# Docker Compose Hello World
We will build a simple Python web application running on Docker
Compose. The application uses the Flask framework and maintains a hit counter in
Redis. While the sample uses Python, the concepts demonstrated here should be
understandable even if you're not familiar with it.

## Install Python
Red Hat removed Python from the Default Install, so we will install it:

```shell
sudo dnf install python3
```

Set Python Command to run Python3
```shell
sudo alternatives --set python /usr/bin/python3
```

Verify Install and Python Command runs Python3
```shell
python --version
```

## Step 1: Setup

Define the application dependencies.

1. Create a directory for the project:

   ```console
   $ mkdir composetest
   $ cd composetest
   ```

2. Create a file called `app.py` in your project directory and paste this in:

  ```shell
  vi app.py
  ```

  ```python
   import time

   import redis
   from flask import Flask

   app = Flask(__name__)
   cache = redis.Redis(host='redis', port=6379)

   def get_hit_count():
       retries = 5
       while True:
           try:
               return cache.incr('hits')
           except redis.exceptions.ConnectionError as exc:
               if retries == 0:
                   raise exc
               retries -= 1
               time.sleep(0.5)

   @app.route('/')
    def hello():
       count = get_hit_count()
       return 'Hello World! I have been seen {} times.\n'.format(count)
  ```
    
 In this example, `redis` is the hostname of the redis container on the application's network. We use the default port for Redis, `6379`.

  > Handling transient errors
  >
  > Note the way the `get_hit_count` function is written. This basic retry
  > loop lets us attempt our request multiple times if the redis service is
  > not available. This is useful at startup while the application comes
  > online, but also makes our application more resilient if the Redis
  > service needs to be restarted anytime during the app's lifetime. In a
  > cluster, this also helps handling momentary connection drops between
  > nodes.

3. Create another file called `requirements.txt` in your project directory and
   paste this in:

   ```shell
   vi requirements.txt
   ```

   ```text
   flask
   redis
   ```

## Step 2: Create a Dockerfile

In this step, you write a Dockerfile that builds a Docker image. The image
contains all the dependencies the Python application requires, including Python
itself.

In your project directory, create a file named `Dockerfile` and paste the
following:

```shell
vi Dockerfile
```

```dockerfile
# syntax=docker/dockerfile:1
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run"]
```

This tells Docker to:

* Build an image starting with the Python 3.7 image.
* Set the working directory to `/code`.
* Set environment variables used by the `flask` command.
* Install gcc and other dependencies
* Copy `requirements.txt` and install the Python dependencies.
* Add metadata to the image to describe that the container is listening on port 5000
* Copy the current directory `.` in the project to the workdir `.` in the image.
* Set the default command for the container to `flask run`.

## Step 3: Define services in a Compose file

Create a file called `docker-compose.yml` in your project directory and paste
the following:

```shell
vi docker-compose.yml
```

```yaml
version: "{{ site.compose_file_v3 }}"
services:
  web:
    build: .
    ports:
      - "5000:5000"
  redis:
    image: "redis:alpine"
```

This Compose file defines two services: `web` and `redis`.

### Web service

The `web` service uses an image that's built from the `Dockerfile` in the current directory.
It then binds the container and the host machine to the exposed port, `5000`. This example service uses the default port for
the Flask web server, `5000`.

### Redis service

The `redis` service uses a public [Redis](https://registry.hub.docker.com/_/redis/)
image pulled from the Docker Hub registry.

## Step 4: Build and run your app with Compose

1. From your project directory, start up your application by running `docker-compose up`.

   ```shell
   sudo docker-compose up
   ```

   Compose pulls a Redis image, builds an image for your code, and starts the
   services you defined. In this case, the code is statically copied into the image at build time.

2. Enter http://localhost:5000/ in a browser to see the application running.

   ```console
   Hello World! I have been seen 1 times.
   ```

   ![hello world in browser](images/quick-hello-world-1.png)

3. Refresh the page.

   The number should increment.

   ```console
   Hello World! I have been seen 2 times.
   ```

   ![hello world in browser](images/quick-hello-world-2.png)

4. Switch to another terminal window, and type this command to list local images.

  ```shell
  sudo docker image ls
  ```

   Listing images at this point should return `redis` and `web`.

   ```console
   REPOSITORY        TAG           IMAGE ID      CREATED        SIZE
   composetest_web   latest        e2c21aa48cc1  4 minutes ago  93.8MB
   python            3.4-alpine    84e6077c7ab6  7 days ago     82.5MB
   redis             alpine        9d8fa9aa0e5b  3 weeks ago    27.5MB
   ```

   You can inspect images with `docker inspect <tag or id>`.

5. Stop the application by hitting CTRL+C or running this in the second terminal window:

    ```shell
    sudo docker-compose down
    ```

## Step 5: Edit the Compose file to add a bind mount

Edit `docker-compose.yml` in your project directory to add a
[bind mount](../storage/bind-mounts.md) for the `web` service:

```shell
vi docker-compose.yml
```

```yaml
version: "{{ site.compose_file_v3 }}"
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
    environment:
      FLASK_ENV: development
  redis:
    image: "redis:alpine"
```

The new `volumes` key mounts the project directory (current directory) on the
host to `/code` inside the container, allowing you to modify the code on the
fly, without having to rebuild the image. The `environment` key sets the
`FLASK_ENV` environment variable, which tells `flask run` to run in development
mode and reload the code on change. This mode should only be used in development.

## Step 6: Re-build and run the app with Compose

From your project directory, build the app with the updated Compose file, and run it.

```shell
sudo docker-compose up
```

Check the `Hello World` message in a web browser again, and refresh to see the
count increment.


## Step 7: Update the application

Because the application code is now mounted into the container using a volume,
you can make changes to its code and see the changes instantly, without having
to rebuild the image.

Goto your second terminal window or tab.  Change the greeting in `app.py` and save it. For example, change the `Hello World!` message to `Hello from Dinohead!`:

```shell
vi app.py
```

```python
return 'Hello from Dinohead! I have been seen {} times.\n'.format(count)
```

Refresh the app in your browser. The greeting should be updated, and the
counter should still be incrementing.

## Step 8: Experiment with some other commands

If you want to run your services in the background, you can pass the `-d` flag
(for "detached" mode) to `docker-compose up` and use `docker-compose ps` to
see what is currently running:

```shell
sudo docker-compose up -d

sudo docker-compose ps
```

The `docker-compose run` command allows you to run one-off commands for your
services. For example, to see what environment variables are available to the
`web` service:

```shell
sudo docker-compose run web env
```

See `docker-compose --help` to see other available commands.

If you started Compose with `docker-compose up -d`, stop
your services once you've finished with them:

```shell
sudo docker-compose stop
```

You can bring everything down, removing the containers entirely, with the `down`
command. Pass `--volumes` to also remove the data volume used by the Redis
container:

```shell
sudo docker-compose down --volumes
```

At this point, you have seen the basics of how Compose works.  Next, will look at deploying an Application consisting of 2 Containers used in DoD today.
