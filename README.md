# About this Repo

This is a fork to add 32-bit support so I can run this on my badly-configured raspberrypi. This repo will be updated once in a while, if you see that I forgot to rebase this just make an issue.

To build the image:
- run update.py:
```bash
python3 update.py
```
- build the docker image:
```bash
docker build ./<version>/<variant>
```
for example:
```bash
docker build ./1.42/apache
```
At the end of the logs docker will give you a line like this:
```
Successfully built 8ba65985b840
```
That random string of letters and numbers (`8ba65985b840` in this case) is the name of the image. You should use this as the image for your container, for example as value for the `image:` field in your `docker-compose.yml`.


# Just because: A guide on how to install MediaWiki using a docker container
This is a simple guide for if you want to install the simplest possible MediaWiki install using docker, so without anything fancy like load-balancers or reverse-proxys (you can always add those later). Just MediaWiki. Note this expects a certain level of knowledge, if you don't understand some step this may not be the right guide.  
Also note this guide is not specifically focused on this repo, but will have info for if you are using this repo. If you are not on a 32-bit devide, you can ignore those notes.

### Step 0: make a directory
Make a directory to work in. Everything we'll be doing here should be done in that directory: all files should be placed relative to that directory, all commands should be ran in that directory. I called mine `mediawiki`.

### Step 1: the `docker-compose.yml` file
Here's the file we'll start with:
```yml
services:
  web:
    image: <imagename>
    ports:
      - <port>:80
    volumes:
      #- ./LocalSettings.php:/var/www/html/LocalSettings.php
      - database:/var/www/data
      - images:/var/www/html/images
      - ./extensions:/var/www/html/extensions
volumes:
    database:
    images:
```
Make sure to replace `<port>` with the port you want to run MediaWiki on, I personally use 8005 and then point a reverse-proxy (using caddy) to that. I'd recommend setting a reverse-proxy up, but I won't describe how to do that here (but caddy is the eaziest imo).  
Also replace the `<imagename>` with the name of your image. If you're running a 64-bit server, you can just use `mediawiki` as image. If you are using 32-bit, read the instructions above on how to build the image, and use the string of random letters and numbers you got from that as image name.
So in my case:
```yml
services:
  web:
    image: 756bcba9c481
    ports:
      - 8005:80
    volumes:
      #- ./LocalSettings.php:/var/www/html/LocalSettings.php
      - database:/var/www/data
      - images:/var/www/html/images
      - ./extensions:/var/www/html/extensions
volumes:
    database:
    images:
```

### Step 2: setup
Now start the stack using
```bash
docker compose up
```
Note this will attach to the logs of the server.  
Navigate to the wiki in your browser. Walk though the setup. When asked, select `SQlite` as database type. When you are done with the setup, the page will automatically start the download for the `LocalSettings.php` file. Put that file in the same directory as your `docker-compose.yml`. Then edit the `docker-compose.yml` file again, and remove the `#` in front of `- ./LocalSettings.php:/var/www/html/Localsettings.php`. Then run the following two commands:
```bash
docker compose down
docker compose up -d
```
This will stop the server, and start it again (this time in the background). Now you're done (except I really recommend putting a reverse-proxy in front of it, do that as well if you are going to publish (also called port-forward) the port this wiki is using).


# Original Readme

This is the Git repo of the Docker [official image](https://docs.docker.com/docker-hub/official_repos/) for [mediawiki](https://registry.hub.docker.com/_/mediawiki/). See [the Docker Hub page](https://registry.hub.docker.com/_/mediawiki/) for the full readme on how to use this Docker image and for information regarding contributing and issues.

The full readme is generated over in [docker-library/docs](https://github.com/docker-library/docs), specifically in [docker-library/docs/mediawiki](https://github.com/docker-library/docs/tree/master/mediawiki).

Do not edit the `Dockerfile`s directly. Changes should be made in the `Dockerfile-*.template` files and applied by running `update.py`.
The project is also running `update.py` via a scheduled github action once per day.

See a change merged here that doesn't show up on the Docker Hub yet? Check [the "library/mediawiki" manifest file in the docker-library/official-images repo](https://github.com/docker-library/official-images/blob/master/library/mediawiki), especially [PRs with the "library/mediawiki" label on that repo](https://github.com/docker-library/official-images/labels/library%2Fmediawiki). For more information about the official images process, see the [docker-library/official-images readme](https://github.com/docker-library/official-images/blob/master/README.md).

Please file bug reports on [Phabricator](https://phabricator.wikimedia.org/project/view/3094/) and not on GitHub.
<!-- THIS FILE IS GENERATED BY https://github.com/docker-library/docs/blob/master/generate-repo-stub-readme.sh -->
