+++
date = "2014-10-09"
title = "Getting Started Part 1"
tags= ["intro"]
draft=false
indexes= ["tags"]

[Params]
	subtitle="Installing And Setting Up Red Skull"

[author]
  email = "therealbill@me.com"
  name = "Bill Anderson"

+++


# First Things First: Getting Red Skull

The quickest route to a running Red Skull is to 1) have Go already
installed and 2) use Go's tools. This document assumes you have Go
already. If not, go install Go and return, we'll wait.

With go installed you have to have Red Skull's Go dependencies installed.
If you use [gpm](https://github.com/pote/gpm) you can simply use the
following commands:

```shell
go get github.com/therealbill/redskull 
cd $GOPATH/src/github.com/therealbill/redskull 
gpm install 
go build
```

Otherwise you can use this sequence of commands:
```shell
go get github.com/therealbill/redskull 
cd $GOPATH/src/github.com/therealbill/redskull 
for x in `cat Godeps`; do
go get $x 
go build
```

Both of them will result in you being able to run `./redskull` and then
visit [the UI](http://localhost:8000) on your system. However there are
a few other things you'll need for it to be useful. Once you have the
`redskull` binary, jump to [Sentinel Setup](#toc_1)

If you're doing development work, it is recommended you do it in the
`$GOPATH/src/github.com/therealbill/redskull` directory rather than
using a different directory. However, if you have a directory where you
store your code checkouts/projects it is better to symlink from that
directory to the GOPATH based one. This is a Go development practice and
it helps Go with imports and test paths.


# Sentinel Setup

Red Skull doesn't require much in the way of configuration.Red Skull
uses the environment for configuration rather than a file, and all
variables it uses for configuration begin with `REDSKULL_`. The defaults
tend to follow standard practices so are usually fine as they are.


## Finding The Config

By default Red Skull will look for the Sentinel config file at
/etc/redis/sentinel.conf. If you have a different name or location
export the full path and name of the file to the environment variable
`REDSKULL_SENTINELCONFIGFILE`. For example if your sentinel config file
is in `/tmp/sentinel-1.conf`, you could do the following:
`REDSKULL_SENTINELCONFIGFILE=/tmp/sentinel-1.conf ./redskull` to tell
Red Skull where the config is.


## Configuring Sentinel

Red Skull reads the sentinel configuration file on startup to bootstrap
it's knowledge of your setup as well as a means for accessing
authentication information for pods under management. Fortunately it
doesn't require much in the config file which isn't already needed. It
will read in the `port` directive of course to know what port to use but
will also require the `bind` directive to be present.

The `bind` directive is used not just to know what IP to use but also to
identify the sentinel Red Skull is running on. When sentinels talk about
themselves they are identified by their connection string using the
format `ip:port`. Thus by pulling the bind directive out we know which
one is 'local'. 

# Conclusion

If you've followed the above steps and made sure to set up your sentinel
config file with the bind directive then congratulations! You should now
be able to run the following command from the redskull directory and be
presented with a functional redskull instance: `cd
$GOPATH/src/github.com/therealbill/redskull && ./redskull`

How to navigate and use the web front end will be the subject of part 2.

