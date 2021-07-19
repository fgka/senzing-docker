# [Senzing](https://senzing.com/) docker deployment

This is to make it easier to build, deploy, and run docker based Senzing on your host.

# DISCLAIMER

I am not affiliated to neither Senzing nor Docker or any involved components.
I will not provide any support to Senzing products and this script is given in a "as-is".

If you are not sure about this, please do not use it.

# Supported architectures **INTEL BASED ONLY** 

* macOS;
* Linux based.

I'm aware that some people are able to run `amd64` images on macOS docker through rosetta.
This, however is not yet (as for 2021-07-19) a stable release for docker.

Source: https://docs.docker.com/docker-for-mac/apple-silicon/

# Tested environments

This has been successfully tested on (stand of 2021-07-14):

* macOS Big Sur (11.4);
* Ubuntu 21.04.


# Requirements

## macOS:

* [Docker desktop](https://www.docker.com/);
* [git](https://git-scm.com/);
* [lsof](https://en.wikipedia.org/wiki/Lsof);
* [yq](http://mikefarah.github.io/yq/).

### Install

```bash
brew install docker git lsof yq
```

Note: you have to go to [Docker](https://www.docker.com/) and install 
Docker desktop.

## Linux:

* [Docker](https://www.docker.com/);
* [Docker compose](https://www.docker.com/);
* [git](https://git-scm.com/);
* [lsof](https://en.wikipedia.org/wiki/Lsof);
* [yq](http://mikefarah.github.io/yq/).

### Install

```bash
sudo apt install -y docker docker-compose git lsof snapd
sudo snap install yq
```

# Get the script

```bash
curl https://raw.githubusercontent.com/fgka/senzing-docker/main/run-senzing.sh \
  -o ${HOME}/run-senzing.sh

chmod 755 ${HOME}/run-senzing.sh
```

# Run the script

You can get the list of options and current suggested usage by issuing:

```bash
${HOME}/run-senzing.sh -h
```

It should give you an output similar to:

```
[INFO] Checking requirements
[INFO] Using docker found in /usr/local/bin/docker
[INFO] Using git found in /usr/local/bin/git
[INFO] Using lsof found in /usr/sbin/lsof
[INFO] Using yq found in /usr/local/bin/yq

Usage:
./run-senzing.sh \
  [--git-repo GIT_REPO_URL] \
  [--git-local-dir GIT_CLONE_LOCAL_DIR] \
  [--sz-volume-dir SENZING_LOCAL_VOLUME_DIR] \
  [--force] \
  [--stop-all] \
  [--development] \
  [--run] \
  [--I-accept-senzing-eula]

Example:
./run-senzing.sh \
  --force \
  --stop-all \
  --I-accept-senzing-eula

./run-senzing.sh \
  --run \
  --stop-all \
  --I-accept-senzing-eula

./run-senzing.sh \
  --git-repo https://github.com/senzing/docker-compose-demo.git \
  --git-local-dir ${HOME}/senzing-docker \
  --sz-volume-dir ${HOME}/senzing-volume \
  --force \
  --stop-all \
  --I-accept-senzing-eula

```

## Deploy (first time)

To deploy, i.e., get all docker images built and deployed, the easiest way is:

```bash
./run-senzing.sh \
  --force \
  --stop-all \
  --I-accept-senzing-eula
```

## Run (subsequent times)

After you deploy, you can simple add the `--run` to the previous command and
it will skip the install step and jump into bringing your deployed version up.

```bash
./run-senzing.sh \
  --run \
  --stop-all \
  --I-accept-senzing-eula
```

# Where is my stuff

You can specify the location of your deployment with additional CLI arguments, which we discourage since you will have keep these options to run as well.

## Default locations

The defaults are the following:

* `https://github.com/senzing/docker-compose-demo.git` as the source of docker definitions;
* `${HOME}/senzing-docker` for git content cloned;
* `${HOME}/senzing-volume` for holding the data used by Senzing, like:
  * PostgreSQL database;
  * RabbitMQ queue;
  * Senzing data.

# CLI Arguments

There are multiple CLI arguments and they are:

* `-h|--help` pulls the help summary (already depicted above);
* `-g|--git-repo` the main Senzing provided git repo for docker compose.  Useful if you have your own fork with some tweaks;
* `-d|--git-local-dir` to set where to clone the git repo into;
* `-v|--sz-volume-dir` where to hold the runtime persistence data;
* `-f|--force` will overwrite any local clone of the git repo. Useful to avoid having to clean the clone dir before cloning it;
* `--development` when running Senzing will also bring up the non-essential containers, like:
  * Swagger UI;
  * Jupyter notebook server;
  * Sample data ingestion (provided by Senzing).
* `-r|--run` assumes that all has been deployed and skip right to bringing the containers up;
* `-s|--stop-all` if there are any containers running, will stop them before doing anything (useful if you want re-start all);
* `--I-accept-senzing-eula` always required to comply with Senzing usage terms.

## How to run with custom locations

We do not recommend that you do, but if you must, below is an example:

### Data in `/opt/senzing`:

You must have write permissions to the target directory.

#### Deploy

```bash
./run-senzing.sh \
  --force \
  --stop-all \
  --sz-volume-dir /opt/senzing \
  --I-accept-senzing-eula
```

#### Run

```bash
./run-senzing.sh \
  --run \
  --stop-all \
  --sz-volume-dir /opt/senzing \
  --I-accept-senzing-eula
```

