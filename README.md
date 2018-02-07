# nubis-ctl

This repository contains a few tools designed to make working in  Nubis account
a bit simpler. The main tool is ```nubis-ctl``` which is what this README will
discuss. There are some additional tools in the ```/bin``` directory which are
documented [there](/bin/README.md).

## Download the tool

In order to run this tool you will first need a copy of it. There are several
options for doing this.

### Download the raw file

This is a fine option if you are just testing things out and do not intend to
update the tool or contribute to the project.

``` bash

curl https://raw.githubusercontent.com/nubisproject/nubis-ctl/master/nubis-ctl \
    > nubis-ctl
chmod +x nubis-ctl

```

### Full Clone

If you will be working with Nubis or contributing code, I recommend you simply
clone the entire repository. This will enable you to update in a simpler maner.
This is also necessary if you are interested in taking advantage of any of the
tools in the ```/bin``` directory.

```bash

git clone https://github.com/nubisproject/nubis-ctl.git

```

## Set up your PATH

The next step is to get this file located somewhere on your path. There are
countless ways to accomplish this and I will show you three here.

### Create a symlink

This is my favorite option and the one I personally use. I simply create a
symlink into my users ```~/bin``` directlry. It is not important where you
simlink it, the location simply needs to already exist in your PATH. Check your
path by ```echo $PATH```. Then :

```bash

ln -s /path/to/nubis-ctl ~/bin

```

### Create a bash alias

If you would rather not maintain a symlink, you can create a bash alias like so:

```bash

export alias nubis-ctl="/path/to/nubis-ctl"

```

In order for this to persist you will need to put into one of your shell
initialization files. I leave that as an excercise for the reader.

### Alter your PATH

Finally you can alter your PATH to include the location where you placed the
nubis-ctl file.

```bash

export PATH=/path/to/location/of/file:$PATH

```

As in the previous example, you will need to put that in one of your shells
initialization files if you want it to persist.

## Configure the Nubis Account

When running the nubis-ctl tool, you will always need to tell it what Nubis
account to operate in. There are three ways of accomplishing that.

First you can simply execute the script and follow the prompts.

Second, you can pass it in using the ```--account NAME``` flag.

Finally you can set a shell variable ```export NUBIS_ACCOUNT='NAME'```.

## Run the tool

The tool is actually a simple wrapper script around other tools which have been
built into Docker images containing all of the necessary dependancies. Each of
the commands will download the container, if necessary, and pass options into
these containers . The simplest method is to simply ask for help like so:

```bash

nubis-ctl --help

nubis-ctl build --help

nubis-ctl deploy --help

```

## Fin

That should be all the info you need. If you run into any issue or have any
trouble feel free to reach out to us. We are happy to help and are quite
interested in improving the project in any way we can. We are on mozilla.slack.com
in #nubis-users or you can reach us on the mailing list at
nubis-users[at]googlegroups.com
