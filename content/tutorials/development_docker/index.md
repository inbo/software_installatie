---
title: "Building Containers with Docker and Podman"
description: "Introduction to containerization and the practical use of docker-like tools."
date: "2025-02-04"
authors: [falkmielke]
categories: ["development", "open science"]
tags: ["development", "open science"]
number-sections: false
params:
  math: true
format:
  html:
    toc: true
    html-math-method: katex
  hugo-md:
    toc: true
    preserve_yaml: true
    html-math-method: katex
output:
  hugo-md:
    preserve_yaml: true
    variant: gfm+footnotes
  html:
    variant: gfm+footnotes
---


You might have heard about "containerization" with [**Docker**](https://docs.docker.com).
Docker has been labeled "the *Holy Grail* of reproducibility" in [The Open Science Manual by Claudio Zandonella Callegher and Davide Massidda (2023)](https://arca-dpss.github.io/manual-open-science/docker-chapter.html).
Although containerization is an immensely useful Open Science tool worth striving for, the *Holy Grail* is an inaccurate metaphor, because
(i) Docker is easy to find and accessible;
(ii) Docker alone does not make a reproducible workflow;
(iii) Docker has issues, some of them mitigated by "Podman".

In this tutorial, I demonstrate step-by-step how to set up and deploy a **custom container** with Docker or Podman.
This is intended to be a rather general test case, serving for later configuration of more specific container solutions.
For example, you will learn how to spin up an existing `rocker/rstudio` container, and even modify it with additional system components and libraries.
I follow other tutorials available online, and try to capture their essence for an INBO-context.
Hence, this is just an assembly of other tutorials, with references - no original ideas to be found below, but nevertheless some guidance.

On Windows, installation, configuration, and management of containers runs via the `docker desktop` app.
However, this tutorial also covers (and in fact focuses on) the terminal-centered steps to be executed on a linux computer or within a WSL.

I also present **Podman** as a full replacement for docker, and recommend to give it a try.

Generally, if you are an INBO user, it is recommended to contact and involve your ICT department for support with the setup.

**References:**

-   <https://docs.docker.com>
-   <https://podman.io/docs>, <https://github.com/containers/podman/blob/main/docs/tutorials/podman-for-windows.md>
-   <https://wiki.archlinux.org/title/Podman>
-   <https://jsta.github.io/r-docker-tutorial/02-Launching-Docker.html>
-   <https://medium.com/@geeekfa/docker-compose-setup-for-a-python-flask-api-with-nginx-reverse-proxy-b9be09d9db9b>
-   <https://testdriven.io/blog/dockerizing-flask-with-postgres-gunicorn-and-nginx>
-   <https://arca-dpss.github.io/manual-open-science/docker-chapter.html>

# Installation

The installation procedure [is documented here](https://docs.docker.com/install).

Docker comes with the *Docker Desktop* app.
That app by itself is trivial and hardly worth a tutorial.

## windows

Navigate to [the download site for docker on windows](https://docs.docker.com/desktop/setup/install/windows-install).
Download the "App" (newspeak for: graphical user interface to a software tool).
Install it.

*Note for INBO users:* you should select Hyper-V, instead of WSL, against recommendation (WSL is discouraged/disabled by enterprise settings; however, ICT might help).
You probably do not have admin rights, which is good.
To re-iterate: **ask our friendly ICT helpdesk for support right away.**

<figure>
<img src="../../images/tutorials/development_docker/docker_desktop1.jpg" alt="desktop app" />
<figcaption aria-hidden="true">desktop app</figcaption>
</figure>

Side rant collection:

-   Installation of Docker Desktop does not allow you to specify an install path (might have missed it).
-   `&'C:\Program Files\Docker\Docker\Docker Desktop.exe'` only runs with admin privileges.
-   It is bloated to more than 2GB (compared to the installed size of `docker` and `docker-buildx` on linux \<200MB; What A GUI!).
-   The first thing it does is prompt you for an unnecessary login (smells like telemetry).
-   The second thing it does is annoy you with a survey (explicit telemetry).
-   It is full of uninvited ads ("Docker Build Cloud", "To access the latest features, sign in."/don't!).

If you want some dignity, learn and use the terminal commands below.
I label them "linux", but they are, in fact, OS-agnostic.

On linux, that same `docker-desktop` [is available for installation](https://docs.docker.com/desktop/setup/install/linux).
But, why would you?

## terminal

On the windows terminal or linux shell, you can install `docker` as a terminal tool.


{{% callout note %}}
On windows, this comes bundled with the App; the steps below are not necessary.
However, note that you need to run a terminal *as administrator*.
{{% /callout %}}

``` sh
sudo apt update && sudo apt install docker docker-buildx # debian-based
# sudo pacman -Sy docker docker-buildx # arch linux
```

For users to be able to use docker, they must be in the "docker" group.
(Insert your username at `<your-username>`.)

``` sh
sudo usermod -a -G docker <your-username>
```

For this change to take effect, log off and log in again and restart the docker service if it was running.

Containers are managed by a system task ("service" and "socket") which need to be started.
Most likely, your linux uses `systemd`.
Your system can start and stop that service automatically, by using `systemctl enable <...>`.  
However, due to [diverse](https://docs.docker.com/engine/security) [security](https://github.com/moby/moby/issues/9976) [pitfalls](https://snyk.io/blog/top-ten-most-popular-docker-images-each-contain-at-least-30-vulnerabilities), it is good practice to **not keep it enabled** permanently on your system.

On a `systemd` system, you can start and stop docker on demand via the following commands (those will ask you for `sudo` authentification if necessary).

``` sh
systemctl start docker

systemctl status docker # check status

systemctl stop docker.socket
systemctl stop docker.service
```

You can check the docker installation by confirming the version at which the service is running.

``` sh
docker --version
```

Congratulations: now the fun starts!

# Existing Containers: `run`

## rationale

Docker is about assembling and working in containers.
"Living" in containers.
Or, rather, you can think of this as living in a ["tiny home", or "mobile home"](https://parametric-architecture.com/tiny-house-movement).
Let's call it a fancy caravan.
The good thing is that you get to pick a general design and to choose all details of the interior.

The best thing: if you feel like you do not have the cash, time, or talent to build your own home, you can *of course* use someone else's.
There are a gazillion **docker images available for you** on [docker hub](https://hub.docker.com).

## example

For example[^1], there are docker images with [rstudio server](https://posit.co/download/rstudio-server) pre-installed:

-   <https://hub.docker.com/r/rocker/rstudio>

{{% callout note %}}
If you control containers via the desktop app, simply search, pull, and run it.
{{% /callout %}}

<figure>
<img src="../../images/tutorials/development_docker/docker_desktop2.jpg" alt="desktop app: run" />
<figcaption aria-hidden="true">desktop app: run</figcaption>
</figure>

Otherwise, execute the following script (*windows*: use an administrator terminal).
If it does not find the resources locally, docker will download and extract the image from dockerhub[^2].

``` sh
docker run --rm -p 8787:8787 -e PASSWORD=YOURNEWPASSWORD rocker/rstudio
```

-   The `--rm` flag makes the docker image non-permanent, i.e. disk space will be freed after you close the container.
-   The port specified at `-p` is the one you use to access this local container server (the `-p` actually maps host- and container ports). You have to specify it explicitly, otherwise the host system will not let you pass (`:gandalf-meme:`).
-   The `-e` flag allows you to specify a password, but if you do not specify one, this container will provide a random password upon startup (read the terminal output).

<figure>
<img src="../../images/tutorials/development_docker/docker_run.jpg" alt="run" />
<figcaption aria-hidden="true">run</figcaption>
</figure>

You are now running (`run`) a `rocker/rstudio` server instance on your `localhost`, i.e. your computer.
You can access it via a browser, going to <localhost:8787>, with the username `rstudio` and your chosen password.

You can shut down the container with the keyboard shortcut `[ctrl]+[C]` (probably `[ctrl]+[Z] [Return]` on windows).

## file access

The downside of this is that your container is isolated (well... at least to a certain degree).

To store files locally, i.e. on the host machine, without storing the bloated container, you will have to map a virtual path on the container to a local drive on your computer.
(Linux people will be familiar with the concept of "mounting" and "linking" storage locations.)

Docker `run` brings the `-v` flag for this.
Suppose you have an R project you would like to work on, stored, for example, in this path:

-   `/data/git/coding-club`

Then you can link this to your containers' home folder via the following command.

``` sh
# windows syntax, mapping on `D:\`
docker run --rm -p 8787:8787 -v //d/data/git/coding-club:/home/rstudio/coding-club rocker/rstudio

# linux syntax
docker run --rm -p 8787:8787 -v /data/git/coding-club:/home/rstudio/coding-club rocker/rstudio 
```

Again, navigate to <localhost:8787>, *et voilà*, you can access your project and store files back in your regular folders.

## limitation

This is a simple and quick way to run R and RStudio in a container.

However, there are limitations:

> **Warning**
>
> -   You have to live with the R packages provided in the container, or otherwise install them each time you access it...
> -   ... unless you make you container permanent by omitting the `--rm` option. Note that this will cost considerable disk space, will not transfer to other computers (the original purpose of docker), and demand occasional updates. (Still the best choice.)
> -   Speaking of updates: it is good practice to keep software up to date. Occasionally update or simply re-install your docker image and R packages to get the latest versions.
> -   You should make sure that the containers are configured correctly and securely. This is especially important with server components which expose your machine to the internet.
> -   There is a performance penalty from using containers: in inaccurate laymans' terms, they emulate (parts of a) "computer" inside your computer.

On the performance issue: I attempted this on my local laptop with matrix multiplication.

``` r
# https://cran.r-project.org/web/packages/rbenchmark/rbenchmark.pdf
# install.packages("rbenchmark")

test <- function(){
  # test from https://prdm0.github.io/ropenblas/#installation
  m <- 1e4; n <- 1e3; k <- 3e2
  X <- matrix(rnorm(m*k), nrow=m); Y <- matrix(rnorm(n*k), ncol=n)
  X %*% Y
}

benchmark(test())
```

In the terminal:

        test replications elapsed relative user.self sys.self user.child sys.child
    1 test()          100  22.391        1    83.961   65.291          0         0

In the container:

        test replications elapsed relative user.self sys.self user.child sys.child
    1 test()          100  26.076        1   102.494   153.89          0         0

Now, the *good news* is that the difference is not by orders of magnitude, which indicates that the chosen rocker image integrated the same good `blas` variant I installed on my computer (`blas-openblas`).

The *bad news* is that we still still a hit of `-20%` performance, which is considerable.

This is just a single snapshot on a laptop, and putatively `blas`-confounded.
Feel free to systematically and scientifically repeat the tests on your own machine.

# Custom Containers: `build`

(Here follows somewhat advanced stuff. Nevertheless, be brave and give it a read!)

## rationale

One advantage of a docker container is its mobility: you can "bring it with you" to other workstations, host it for colleagues or readers, use cloud computing, mostly without having to worry about installation of the components.
This is a matter of good open science practice.
But it also pays off in complicated server setups and distributed computing.

A standardized container from [dockerhub](https://hub.docker.com) is a good start.
However, you will probably require personalization.
As a use case, imagine you would like to have an RStudio server which comes with relevant inbo packages pre-installed (e.g. [`inbodb`](https://inbo.github.io/inbodb), [`watina`](https://inbo.github.io/watina); *cf.* [contaINBO](https://github.com/inbo/contaINBO)).

I will return to this use case below.
To explore the general workings of `docker build`, let's turn to more web-directed tasks for a change.

{{% callout note %}}
With Docker Desktop, you have the graphical interface for "builds".
This might fall under the extended functionality which requires a login.

Yet even without a login, you *can* proceed via a terminal, as below.
Once you create a `Dockerfile` and build it, it will appear in the GUI.
{{% /callout %}}

<figure>
<img src="../../images/tutorials/development_docker/docker_winbuild.jpg" alt="build on windows" />
<figcaption aria-hidden="true">build on windows</figcaption>
</figure>

## init: a `flask`

[Python `flask`](https://en.wikipedia.org/wiki/Flask_(web_framework)) is a library which allows you to execute python scripts upon web access by users.
For example, you can use flask to gather information a user provides in an html form, then process and store it wherever you like.

I started from the following examples and tutorials to spin up a flask container, but provide modifications and comments on the steps.

-   <https://docs.docker.com/build/concepts/dockerfile>
-   <https://medium.com/@geeekfa/dockerizing-a-python-flask-app-a-step-by-step-guide-to-containerizing-your-web-application-d0f123159ba2>

> **It all starts with a [dockerfile](https://www.geeksforgeeks.org/what-is-dockerfile).**[^3]

As you will see, the docker file will give you all the design choices to create your own containers.
I think of the docker file as a script which provides all the instructions to set up your container, starting with `FROM` (i.e. which prior container you build upon) to `RUN`ning any type of commands.
Not *any* type, really: we are working on (mysterious, powerful) linux - don't fret, it's easier than you think!

To our `python/flask` example.
A list of the official python containers is [available here](https://hub.docker.com/_/python).
Note that you build every container upon the skeleton of an operating system: I chose [alpine linux](https://en.wikipedia.org/wiki/Alpine_Linux).
(It's *en vogue*.)

The dockerfile resides in your working folder (yet it also defines a [`WORKDIR`](https://stackoverflow.com/a/51066379) from within which later commands are executed).

-   Navigate to a folder in which you intend to store your container(s), e.g. `cd C:\data\docker` (windows) or `cd /data/docker` (linux).
-   Create a file called `Dockerfile`: `touch Dockerfile`.
-   Edit the file in your favorite text editor (`vim Dockerfile`; windows people probably use "notepad").
-   Paste and optionally modify the content below.

<!-- -->

    # Use the official Python image (alpine linux, python 3)
    FROM python:3-alpine

    # install app dependencies
    RUN apk update && apk add --no-cache python3 py3-pip
    RUN pip install flask

    # install app
    COPY hello.py /

    # final configuration
    ENV FLASK_APP=hello
    EXPOSE 8000
    CMD ["flask", "run", "--host", "0.0.0.0", "--port", "8000"]

Note that the following `hello.py` file needs to be present in your working directory (you will be reminded by a friendly error message):

``` python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello, INBO!"
```

With the `Dockerfile` and `hello.py` in place, you can build the container [^4].

``` sh
# on windows, you are already in an administrator terminal
docker build -t my-flask .

# on linux, use sudo if you like or go to "Podman" (rootless)
sudo docker build -t my-flask .
```

<figure>
<img src="../../images/tutorials/development_docker/docker_build.jpg" alt="build" />
<figcaption aria-hidden="true">build</figcaption>
</figure>

List your available container images via the `docker images` command.

You should now see a `python` image, which is the base alpine image we built upon.
There is also a `my-flask`.
Try it!

``` sh
docker run my-flask
```

The terminal should give you an IP and port; because the flask runs in a container, `localhost:8000` will **not work** out-of-the-box.
Instead, in my case, it was `http://172.17.0.2:8000`.
(Sadly, although I could build and run this container on windows, I did not get through via the browser :shrug: but try with port mapping `-p 8000:8000`.)

{{% callout note %}}
So far, so good.
We have used an existing image and added `flask` on top of it.
This works via writing a `Dockerfile` and building an image.
{{% /callout %}}

## Multiple Images: `compose` versus `build`

The above works fine for most cases.
However, if you want to assemble and combine multiple images, or build on base images from multiple sources, you need a level up.

In that case `docker compose` is [the way to go](https://docs.docker.com/compose/gettingstarted).
I did not have the need to try this out, yet, but will return here if that changes.

# Use Case: RStudio with Packages

## rationale

We should be able to apply the above to modify the `rocker/rstudio` server image for our purpose.

Build recipes for some of the INBO packages you might want to include are collected in this repository:

-   <https://github.com/inbo/contaINBO>

Contributions are much appreciated!

## dockerfile

This use case is, in fact, well documented:

-   <https://rocker-project.org/use/extending.html>
-   <https://rocker-project.org/images/versioned/rstudio.html>
-   <https://davetang.org/muse/2021/04/24/running-rstudio-server-with-docker>

The Rocker crew rocks!
They prepared quite [a lot of useful images](https://hub.docker.com/u/rocker), including for example the `tidyverse` or geospatial packages.

Note the syntax in `FROM`: it is `rocker/<image>:<version>`.

    # Use the rocker rstudio image
    FROM rocker/rstudio:latest

    # update the system packages
    RUN apt update \
        && apt upgrade --yes

    # git2rdata requires git
    RUN apt install git libgit2-dev --yes

    # update pre-installed R packages
    # RUN Rscript -e 'update.packages(ask=FALSE)'

    # install package via Rscript
    # (a) via r-universe
    # RUN Rscript -e 'install.packages("watina", repos = c(inbo = "https://inbo.r-universe.dev", CRAN = "https://cloud.r-project.org"))'
    RUN Rscript -e 'install.packages("git2rdata")'

    # (b) from github
    RUN R -q -e 'install.packages("remotes")'
    RUN R -q -e 'remotes::install_github("inbo/INBOmd", dependencies = TRUE)'

It takes some puzzle work to get the dependencies right, e.g. with the `libgit2` dependency (try commenting out that line to get a feeling for build failure).
However, there is hope: (i) the error output is quite instructive (at least for linux persons), (ii) building is incremental, so you can add successively.
It just takes patience.
Remember which system powers your container (Debian/Ubuntu), find help online, and document your progress.

{{% callout note %}}
Dockerfiles offer some room for optimization.
For example, every `RUN` is a "Layer"; you should put stable layers top and volatile layers later.
In principle, it is recommended to combine layers as much as possible.

More here: <https://docs.docker.com/build/building/best-practices>
{{% /callout %}}

Test the image:

``` sh
docker build -t test-rstudio .
```

Run it, as before:

``` sh
docker run --rm -p 8787:8787 -e PASSWORD=YOURNEWPASSWORD test-rstudio
```

Another good practice is to extract modifications in scripts and modularly bring them in to be executed upon installation ([see here](https://stackoverflow.com/q/69167940), [and here](https://rocker-project.org/use/extending.html#install2.r)), via `COPY`.
This makes them available for a more refined version control.
As you know, [version control is key!](https://tutorials.inbo.be/tags/git)

But, on that line, how about private repositories?
More generally, how would we get (personal) data from our host machine to the container?

## data exchange

Arguably, among the rather tricky tasks when working with containers is file exchange.
There are [several options available](https://forums.docker.com/t/best-practices-for-getting-code-into-a-container-git-clone-vs-copy-vs-data-container/4077):

-   `COPY` in the Dockerfile (or `ADD` [in appropriate cases](https://www.docker.com/blog/docker-best-practices-understanding-the-differences-between-add-and-copy-instructions-in-dockerfiles))
-   ["bind mounts"](https://docs.docker.com/engine/storage/bind-mounts)
-   [volumes](https://docs.docker.com/engine/storage/volumes)
-   R's own ways of installing from far (e.g. `remotes::install_github()`)

For the use case of [installing R packages from a private git repo](https://www.geeksforgeeks.org/how-to-clone-private-git-repo-with-dockerfile), there are several constraints:

-   It best happens at build time, to enable all the good stuff: `--rm`, sharing, ...
-   Better keep your credentials (e.g. ssh keys, access tokens) off the container, both system side and [on the R side](https://usethis.r-lib.org/articles/git-credentials.html).
-   On the other hand, updates can often happen by re-building.

In this (and only this) situation, the simple solution is to copy a clone of the repository to the container, and then install it.
The clone should be within the Dockerfile folder.

    # copy the repo
    COPY my_private_repo /opt/my_private_repo

    # manually install dependencies
    RUN R -q -e 'install.packages("remotes", dependencies = TRUE)'

    # install package from folder
    RUN R -q -e 'install.packages("/opt/my_private_repo", repos = NULL, type = "source", dependencies = TRUE)'

This [seems to be good practice](https://stackoverflow.com/questions/23391839/clone-private-git-repo-with-dockerfile/55761914#55761914), for being simple, secure, and well feasible.

# useful commands

We have briefly seen `docker --version`, `docker build`, `docker run`, and there are certainly more settings and tweaks on these commands to learn about.

There are other docker commands which might help you out of a temporary misery.

-   `docker run -it --entrypoint /bin/bash <image>` or `docker run -it <image> /bin/bash` brings you to the shell of a container; you can update, upgrade, or just mess around. Try `bash` or `bin/sh` as alternatives.
-   `docker images` will list your images in convenient table format; the `-q` flag returns only IDs.
-   `docker inspect <image-name or image-id>` brings up all the configuration details about a specific image; you can, for example, find out its docker version and network ip.
-   `docker ps` ("print status") will list all running containers; `docker stop $(docker ps -a -q)` will stop them **all**.
-   `docker rmi <image-name or image-id>` will remove an image; `docker rmi $(docker images -q)` will remove **all** your images. Of course, you get to keep the dockerfiles.

There are a gazillion more to choose and use, the [docker docs](https://docs.docker.com/reference/cli/docker) are your go-to source.

One more note on the `ENTRYPOINT`:
It defines through which terminal or script the user will access the container.
For example, `/bin/bash`, `/usr/bin/bash` or `bin/sh` are the bash (linux terminal).
Rocker images usually enter into an R console, or monitor an RStudio server.
The flask container above runs a script which hosts your website and python.
Anything is possible.
You can define an entrypoint in the Dockerfile (i.e. set a default), or overwrite it on each `run`.

# Podman

## purpose

Whales don't really carry "containers", and "images" are better hung on walls.
Don't fall for the docker marketing!

There are alternatives which mitigate some of the docker limitations and disadvantages.

The most prominent one (or rather the only one *I* looked at, sorry) might be `podman`.
A container is a "pod", they run on a "machine", and this FOSS tool helps you to manage them.
The major advantage of podman is that it can be configured to run **"rootless"**, i.e. without administrator rights [^5].

Podman is [well documented](https://podman.io/docs/installation).
Another reliable source as so often is the [arch linux wiki on podman](https://wiki.archlinux.org/title/Podman), no matter which linux you are on.
On windows, people have succeeded in running Podman through a WSL.

{{% callout note %}}
For windows, there is a convenient "Podman Desktop" GUI which guides you through the installation and setup, including WSL instantiation.
It is intuitive, transparent (telemetry opt-out), backed by RedHat.

Unfortunately, it relies on Windows Subsystem for Linux (WSL), which is not available for INBO users at the moment.

:(

We are working on it.
{{% /callout %}}

## setup

The instructions below were tested on arch linux, but generalize easily.

I follow the `podman` installation instructions for arch linux, to set up a **rootless container environment**.

Installation:

``` sh
pacman -Sy podman podman-docker passt
```

The last one, `passt` (providing `pasta`, yum!), is required for rootless network access.  
Optionally, there is `podman-compose`.

Out of the box, Podman will run *only if you are root*.
This has to be changed to go *rootless* ([see also](https://man.archlinux.org/man/podman.1#Rootless_mode))
The first step is to confirm a required kernel module: check that `unpriviledged_users_clone` is set to one.

``` sh
sysctl kernel.unprivileged_userns_clone
```

Then, configure "subordinate user IDs".
There are detail differences in each linux distro; with some luck, your username is already present in these lists:

``` sh
cat /etc/subuid
cat /etc/subgid
```

If not, you can be admitted to the club of subordinates with the command:

``` sh
usermod --add-subuids 100000-165535 --add-subgids 100000-165535 <username>
podman system migrate
```

We note some useful commands on the way: `podman system ...` and `podman info`.
You might immediately check "native rootless overlays" (has something to do with mounting filesystems in the container):

``` sh
podman info | grep -i overlay
```

Then, networking: pods might need to communicate to each other and to the world.
And, of course, container storage: make sure you know where your containers are stored.
These and more settings are in `/etc/containers/containers.conf` and `/etc/containers/storage.conf`; make sure to scan and edit them to your liking.

## usage

You can use images from `docker.io` with podman.
The only difference from docker is the explicit mention of the source, `docker.io`.
For example:

``` sh
podman search docker.io/alpine
podman pull docker.io/alpine # download a machine
podman run -it docker.io/alpine # will connect to the container
exit
```

## podman rocker

From here, **podman is a full drop-in replacement for docker**; just that you are not forced to grant host system root privileges to containers.

Any `Dockerfile` should work, with the mentioned mini-adjustment to `FROM`.
And you can use any docker image; `docker.io/rocker/rstudio` [is available](https://rocker-project.org/use/rootless-podman.html) (don't forget to specify the port).
You may even write `docker` in the terminal: it will alias to `podman` (via the `podman-docker` package on linux).

``` sh
podman run --rm -p 8787:8787 -e PASSWORD=YOURNEWPASSWORD -v /data/git/coding-club:/root/coding-club docker.io/rocker/rstudio
```

There is another subtle change: the default user to login to `rstudio` is not `rstudio`, but `root`, because you need to have root rights on the container.
You had those before anyways, but now they are confined to within the pod.

{{% callout note %}}
To summarize the Podman experience:

-   **Docker's Dockerfiles like the one above will build as with docker.**
-   You can even stick to the `docker` commands thanks to the `podman-docker` package.
-   There is podman desktop, if you like clicking.
-   Podman is everything docker is, just better.

{{% /callout %}}

Kudos to the podman devs!

# Summary

In this tutorial, I demonstrated the basics of containerization with docker and podman.
There are convenient GUI apps, and sophisticated terminal commands, the latter is much more powerful.

Personally, I find the concept of containerization fascinating, and was surprised how simple and useful of a trick it is.

Containerization offers the advantages of modularity, configurability, transparency (open science: share your rocker file), shared use ...
There are some manageable pitfalls with respect to admin rights and resource limitation.

This was just a quick tour; I brushed over a lot of novel vocabulary with which you will have to familiarize yourself.
Your head might be twisting in a swirl of containers by now.
I hope you find this overview useful, nevertheless.
Thank you for reading!

[^1]: I mostly follow [this tutorial](https://jsta.github.io/r-docker-tutorial/02-Launching-Docker.html).

[^2]: Just like "github" is a server service to store git repositories, guess what: "docker hub" is a hosting service to store docker containers.

[^3]: Here I quoted the docs (<https://docs.docker.com/build/concepts/dockerfile>) before having read them.

[^4]: If you did not install the `docker-buildx` package on linux, you will get a legacy warning.

[^5]: Daniel J. Walsh (2019): "How does rootless Podman work?" <https://opensource.com/article/19/2/how-does-rootless-podman-work>
