+++
date = "2014-10-10"
title = "Getting Started Part 2"
draft=false
tags=["webui","intro"]

[menu.guides]
	name = "Getting Started Pt.2"

[author]
  email = "therealbill@me.com"
  name = "Bill Anderson"

[Params]
	subtitle = "Getting to know the Web UI"
+++


# Becoming Familiar With The UI

This portion of the guide will walk you through the various views of the
UI from the dashboard to the view of individual nodes.

# The Dashboard

Clicking on the Dashboard link takes you to an operational overview of
your Sentinel cluster. It should look something like this: 
<a target="_blank" href="/images/dashboard-view.png">
<img alt="dashboard screenshot" src="/images/dashboard-view.png" width="480px" />
</a>.


## Info Boxes

The boxes at the top of the screen are designed to be seen and understood
across a room. If you see red, you have a problem. The info boxes display
a metric name and it's value. For example if you have pods with an error
condition the "Pods With Errors" box will be red and have the number of
pods with errors listed in it.

## Error States

The intent of this view is to provide an at-a-glance window into any error
states or warning your cluster may have.  Any pods with errors will cause
the Pod Error box to be red and will list the number of pods with errors.

Below the info boxes is a list of pods (if any) with errors. These errors
are broken up into categories identifying the specific error found. A pod
may have multiple errors thus it may be present in multiple tabs. As a
result the total on the left, "All Errors", may not match what you get if
you sum the other numbers.

<a target="_blank" href="/images/errors-view.png">
<img alt="errors view screenshot" src="/images/errors-view.png" width="480px" />
</a>.

By clicking each error name you are presented with list of pods and some
brief information about them, which have that error. You are also
presented with some descriptive text about what the error is and how you
can fix it - either inside or outside of Red Skull. If you click on the
pod name you are taken to the [Pod View](#toc_6)

# Constellation View

This view shows you the stat of the constellation cluster. It Shows you
how many pods and nodes are under management as well as memory. It shows
you the breakout of your pods by memory size and information on each
sentinel in the cluster. 

Available actions from here are to add a pod to be managed and add a
sentinel to the cluster. The name of this view will be changing once the
new term for the 'cluster of sentinels' is determined.

# Pods Listing

The pods view will provide a table of all known and managed pods in this
constellation cluster. The table is filterable, sortable, and paginated.
You can decide the number of entries to display per page as well as sort
by column and filter by name. If you click on the pod name you are taken to
the [Pod View](#toc_6)

<a target="_blank" href="/images/pods-view.png">
<img alt="pods view screenshot" src="/images/pods-view.png" width="480px" />
</a>.

It is important to understand when this view is loaded Red Skull is
actively querying the sentinels for the data. Loading this immediately
after starting Red Skull or during heavy cluster changes can result in it
having slightly "stale" data if you have hundreds of pods to ask about.


# Pod View

By clicking on link to a given pod you are presented with the information
Red Skull knows about the pod. This includes the standard Sentinel bits
such as the current master, any connected slaves, the quorum, etc. but
also includes some calculated and audit-style information.

<a target="_blank" href="/images/pod-view.png">
<img alt="pod view screenshot" src="/images/pod-view.png" width="480px" />
</a>.

## Pod Overview Panel

The primary box runs through a short overview containing the name and the
result of a set of health checks:

* Can the pod fail-over?
* Does the slave have at least as much memory as the master?
* Are there enough *configured* sentinels?
* Are there enough *active* sentinels?

In addition you are presented with the connection information and a link
to view the current master node, the number of connected slaves, and a
memory consumption bar.

Regarding memory consumption, it is a good practice to limit your Redis
instances via the `maxmemory` configuration parameter. This helps prevent
a runaway process from taking down your Redis host. Red Skull consults
this config variable and uses that to determine how much of the allocated
memory is currently in use. It will indicate an warning or error state if you
exceed 60% and 80% of the maximum memory setting respectively. 

## Constellation Panel

To the right of the overview panel is the constellation panel. This panel
simply displays the current active sentinels managing this pod.

## Actions Panel

On the far right is the pod actions panel. This panel displays buttons
which allow you to manage this pod directly. Currently the primary options
are to view the master node, add a slave, force fail-over, reset the pod,
stop managing the pod.

Under certain circumstances these options may change. For example, if the
pod is not able to fail-over the button for that is not visible. If the
pod is currently out of balance (doesn't have enough sentinels), a button
for rebalancing the pod will appear.

## Connected Slaves Table

The table at the bottom of the view lists and links each connected slave
and some replication specific information such as the current offset, the
slave's maximum allowed memory, it's promotion priority, and of course
it's name, IP, and port.

Currently there is a disabled button to remove a slave. In a future
release of Red Skull this button will remove the slave and reset the slave
list in Sentinel.


# Node List

This view shows you a data grid of the known nodes - the masters and the
slaves under sentinel's management. A brief bit of information such as the
version, the last startup, used memory and max memory are displayed. At
the top of the page are info boxes displaying the total number of nodes
under management as well as the number of nodes with errors. Clicking a
node's name will take you to the [Node View](#toc_12).

<a target="_blank" href="/images/nodes-view.png">
<img alt="nodes view screenshot" src="/images/nodes-view.png" width="480px" />
</a>.

# Node View

The node view provides you with information about this specific node. As
Redis exposes quite a bit of information this page is split into tabs,
grouping the information by category.

<a target="_blank" href="/images/node-view.png">
<img alt="node view screenshot" src="/images/node-view.png" width="480px" />
</a>.

## Node Information

The initial tab display essentially the same information as is seen in the
node table on the [Node List](#toc_11). It does add, however, when the
information you are viewing was last retrieved. As of this writing it can
be up to 90 seconds old if there are no errors in retrieving the data.
Otherwise it will turn red so you can easily see the data is old.

## Memory

The memory tab shows you the memory information, as one might expect. It
details the current maximum memory setting, how much is currently in use
and what percentage that represents.

In addition you are presented with the peak memory to aid in broader
memory analysis (you see your usage spikes), the amount of memory consumed
by the Lua system and scripts which have run, and the system memory Redis
has consumed. This latter metric would be the equivalent of "RSS" in
Linux. It represents the data set but also any operation overhead such as
memory used in replication or disk persistence.

## Replication

This tab provides information about connected slaves, the role of the
node, and the synchronization state.

## Command Stats

This view is essentially a table showing what commands are being called on
this node and how much time they have taken in sum as well as on average
what each call takes.

This view will automatically scale from microseconds to milliseconds to
help your brain understand the data better. 

An important understand for this view is that Sentinel issues commands on
the Redis nodes as part of understanding them. Additionally, Red Skull
runs queries on the nodes. Thus an unused pod will still have commands
in this table.  


## Persistence

This tab shows the current state of persistence. If you are persisting to
disk you will see what methods are in use, otherwise it will indicate it
to be a "cache" style configuration. 


# Conclusion

While Red Skull is still quite new, you can see there is already a
significant amount of information and capability in the web interface. Be
sure to check http://redskull.io periodically to see news and details on
new features or improvements in the UI.

In the [part three](/guide/gettingstarted-part3) of this series we will
cover development of and developing Red Skull itself.
