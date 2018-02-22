# nubis-ctl

[![GitHub release](https://img.shields.io/github/release/nubisproject/nubis-ctl.svg)](https://github.com/nubisproject/nubis-ctl/releases/)

This repository contains a few tools designed to make working in  Nubis account
a bit simpler. The main tool is ```nubis-ctl``` which is what this README will
discuss. There are some additional tools in the ```/bin``` directory which are
documented [there](/bin/README.md).

## Installation Instructions

There are a number of options for installing this tool as it is released on
github. The method I will demonstrate here will use git and a symlink.
Feel free to use another method if you desire.

### Git Clone

First, navigate an appropriate location on your file-system. Next, clone the
repository and check out the most current release (tag). Hint: Check out the
version badge at the top.

```bash

git clone https://github.com/nubisproject/nubis-ctl.git
cd nubis-ctl
git fetch --tags
git checkout vX.X.X

```

Using this method makes it quite simple to upgrade in the future.
Simply `git fetch origin` and `git checkout vX.X.X`. (Replacing 'X.X.X' with the
new version number.)

### Create a symlink

The next step is to get this file located somewhere on your path. There are
countless ways to accomplish this, but I will show you my favorite method and
the one I personally use. I simply create a symlink into my users `~/bin`
directly. It is not important where you simlink it, the location simply needs
to already exist in your PATH. Check your path by `echo $PATH`. Then :

```bash

ln -s /path/to/nubis-ctl ~/bin

```

## Operational Instructions

The tool is basically a wrapper script around tools which have been baked into
Docker images containing all of the necessary dependencies. The commands will
download container, when necessary, and pass options into them . Lets get
started with an example.

All of the commands have help:

```bash

nubis-ctl --help

nubis-ctl build --help

nubis-ctl deploy --help

```

As you can see this tool uses the common nested command structure. Each level
in the nest accepts options (--help) and commands (plan).

### Build an AMI

Almost every project will be building an AMI for their application. From the top
level of your project run the following command:

```bash

nubis-ctl --account nubis-training build

```

### Build an AMI in debug mode

If you are having trouble building your AMI you can build in debug mode. In
debug mode you will be asked at every step in the build process to hit [enter]
to continue.

```bash

nubis-ctl --account nubis-training build debug

```

After roughly five steps you will see the source instance launching and then a
few IP addresses. Do not hit enter at this point. It will look similar to this:

```bash

==> amazon-ebs-centos: Launching a source AWS instance...
==> amazon-ebs-centos: Adding tags to source instance
    amazon-ebs-centos: Adding tag: "Name": "Packer Builder"
    amazon-ebs-centos: Instance ID: i-07ee9771638e5f303
==> amazon-ebs-centos: Waiting for instance (i-07ee9771638e5f303) to become ready...
    amazon-ebs-centos: Public DNS: ec2-54-189-197-54.us-west-2.compute.amazonaws.com
    amazon-ebs-centos: Public IP: 54.189.197.54
    amazon-ebs-centos: Private IP: 172.31.13.224
==> amazon-ebs-centos: Pausing after run of step 'StepRunSourceInstance'. Press enter to continue.

```

At this point, you will find a .pem file in the project directory. Using the
`Public IP` address and pem file, you can SSH to the instance.

A few Notes:

* The username during builds is `nubis`.
* The pem file name varies based on the type of instance (RHEL|Ubuntu).
* This uses ~insecure~ ssh options to avoid writing temporary host keys to
  ~/.ssh/known_hosts

```bash

ssh -o 'UserKnownHostsFile=/dev/null' -o 'StrictHostKeyChecking=no' -i ec2_amazon-ebs-centos.pem nubis@54.189.197.54

```

When you are finished working on the instance, simply press [enter] in the build
terminal until the instance terminates. This is necessary (vs. ^c) to make sure
the build instance is not left running in the account, spending money.

### Build an AMI advanced usage

As the commands are all simple wrappers around other commands, you have the full
flexibility of all the underlying commands. To explain this, lets take a
complex example and break it down into its component parts:

```bash

nubis-ctl --account nubis-training build --debug --copy-regions "us-east-1,us-west-2" build --instance_type c3.large --packer-options debug

```

The first part of the command `nubis-ctl --account nubis-training build` invokes
nubis-ctl, passes in the account name to work in and invokes the nubis-builder
docker container.

The next part of the command contains the options and a command for the
nubis-builder container. Namely
`--debug --copy-regions "us-east-1,us-west-2" build` which tells the container
to run in debug mode for lots of verbose output. Then tells it that I want
copies of my AMI in two regions and finally invokes the build command. Normally
it is not required to call build explicitly as it is set in the Dockerfile
(`CMD [ "build" ]`). However as we are overriding this default command it
becomes necessary to include it here.

The final portion of the command passes options to the nubis-builder code
itself. `--instance_type c3.large --packer-options debug` In this example we are
defining a much larger instance size to speed up the build and passing an
option to Packer, instructing it to build in debug mode. Interestingly this
is the exact same way we get into debug mode in the previous section, it is just
hidden behind a sprinkle of magic there.

While this might look a bit complicated at first, it is actually not all that
difficult when you break it down. You just need to remember what scope you are
in. One final note, all of these commands and sub-commands have built in help.
I'll quickly illustrate getting help at each level to illustrate the point:

```bash

nubis-ctl --account nubis-training help

nubis-ctl --account nubis-training build help

nubis-ctl --account nubis-training build build help

```

### Deploy into an account

Under the hood we use Terraform for deployments. The deploy command is really
just a hook into Terraform. This means that all of the commands you are familiar
with can be passed directly to `nubis-ctl deploy`.

```bash

nubis-ctl --account nubis-market deploy (plan|apply|destroy|outputs|help)

```

### Working with accounts

I will mention that this tool also has a command for working with accounts. This
is a sub-command of the deploy command. As this is not a common task I will be
quite brief.

```bash

nubis-ctl --account nubis-training deploy account help

```

## Fin

Hopefully that got you where you need to be. If you run into any issue or have
any trouble feel free to reach out to us. We are happy to help and are quite
interested in improving the project in any way we can. We are on
mozilla.slack.com in #nubis-users or you can reach us on the mailing list at
nubis-users[at]googlegroups.com
