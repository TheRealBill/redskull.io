+++
date = "2014-10-07T00:31:19Z"
draft = false
title = "Deploying RedSkull"
description = "A quick guide on how RedSkull can be deployed"
tags = ["operations"]
[author]
	name="Bill Anderson"
	email="therealbill@me.com"

[menu.guides]
	name="Deploying RedSkull"
	weight="4"

+++


# Introduction

Currently deployment is relatively simple. The first, and most important, thing
you need to know is that currently redskull looks for the HTML directory in the
same directory as the binary. The second is that I've not written a startup
script for it yet. As this is a new project, I will update this guide
as it changes so check back to see any changes.

For this guide I will assume you are building and deploying to a Unix system.
If deploying to a Linux server, build on Linux. If deploying to OSX, build on
OSX. You get the picture here. I'll also assume you know the basic
tools for yur OS. If you are not comfortable and capable at the
command line, find and study some guides or tutorials for your OS.

# Creating a Tarball

After you've clone the repo and changed into it's top-level directory,
run `go build`. It shan't take long. Next up do the following:

```
mkdir -p dist/usr/local/redskull
cp -a redskull html dist/usr/local/redskull
cd dist
tar -czf redskull.tar.gz usr/
```

Now you have a tarball you can extract in the root of your server.
Deploy it to your sentinel servers.


# Configuring Sentinel

The only thing RedSkull must have in the Sentinel config file are the `bind`
and `port` directives. While `port is mandatory for Sentinel, you will need to
ensure `bind <yoursentinel-ip>` is present.

# "Configuring" RedSkull

RedSkull require no configuration provided you are OK with the
following:

- It listens on port 8000
- Your sentinels' configuration file is /etc/redis/sentinel.conf

If you're locaton is different, place it in the environment variable
`"REDSKULL_SENTINELCONFIGFILE` and fire up redskull.

# Start on Bootup

Yeah, I haven not yet written any startup scripts. I'll be writing a
supervisord config for it soonish though. As it is a daemon which
doesn't require any config you can crib off of an existing startup
script, changing the particulars to match your locations and be off
and running.


