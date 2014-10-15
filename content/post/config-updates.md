+++
date = "2014-10-15T08:45:23-05:00"
title = "Configuration Updates"
description = "Configuration updates and Sentinel overrides"

[author]
  email = "therealbill@me.com"
  name = "Bill Anderson"

+++


# Updates to Red Skull Binding Config

I've made some improvements to the ability to determine what IP/Port Red
Skull listens on. By default it will glom on to all interfaces on port
8000. Now you can set the environment variable `REDSKULL_BINDADDRESS` to
an `IP:PORT` string and it will bind to that pair. Alternatively you can
us `REDSKULL_PORT` or `REDSKULL_IP` to set them individually. This is useful
if, for example, you want to set *just* the port number but retain the
"glom onto all interfaces" behavior.

# Bind Directive Override

An additional improvement is the ability to specify the local
sentinel's bind address. This is primarily useful when you don't have a
`bind IP.AD.DR.ESS` directive in your sentinel config file. 

There are a couple reasons you might use this instead of the bind
directive. By default Sentinel will glom onto all IP addresses to bind
to. If sentinel is given a bind directive it will listen *only* on that
interface. This is generally what you want, but under certain
circumstances you may want the default behavior. Red Skull will now let
you specify that address in it's environmental configuration to handle
either case.


# Configurable Template Directory

By default Red Skull expects the `html` directory, where it finds it's
templates, to be in the same directory as the binary. This isn't always
desirable. Now Red Skull will look to `REDSKULL_TEMPLATEDIRECTORY` for
an alternate directory. *NOTE*: this must be the directory which
contains the 'html/' directory, not the path including it.

# Conclusion

These changes will make it easier to customize your deployment of Red
Skull and integrate it into existing Sentinel configurations.


