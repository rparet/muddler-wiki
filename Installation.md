# Installation

There are two primary ways to install muddler for use on your computer.

* [Docker](#docker-image) This method has a couple of distinct advantages, namely:
  * Easy to automatically update
  * No need to have Java installed on your personal machine
  * It's the one I personally use and am most familiar with
* [Basic](#basic-installation)
  * Needs Java
  * Does not update itself
  * Should work fine though

## Docker image

Uses a docker container I maintain so that you don't have to manage your own Java install or muddler updates.

### Docker Prerequisites

* [Docker](https://www.docker.com/)

### Docker Installation steps

* `docker pull demonnic/muddler:latest`
* Create a script to run muddler using docker
  * For a system running bash, you can download the script [HERE](https://raw.githubusercontent.com/demonnic/muddler/main/muddle) and save it anywhere in your path, then make it executable using `chmod +c /path/to/muddle`
  * The main thing is you want to run:
  * `docker run --pull always --rm -it -u $(id -u):$(id -g) -v $PWD:/$PWD -w /$PWD demonnic/muddler`
  * If you do not want it to autoupdate, remove `--pull always` from the command.
  * For Windows without bash, you may need to work on the pathing, and may not need the `-u $(id -u):$(id -g)` portion used to ensure the resulting files have the right user

## Basic Installation

For those who do not want to install Docker but don't mind installing Java8 or higher

### Basic Prerequisites

* [Java8](https://www.java.com/download/) or higher installed and available in your path.

### Basic Installation steps

* Download the latest release from <https://github.com/demonnic/muddler/releases>.
  * For instance, muddle-shadow-1.0.0.zip
* Unzip file to a location on your harddrive, then add the bin directory inside muddle-shadow-0.1 directory you just unzipped to your path. For instance, if you unzipped the file to C:, you would add C:\muddle-shadow-1.0.0\bin to your path.
* The script to run muddler is now in your path.
  * `muddle` for linux and macOS
  * `muddle.bat` for windows
