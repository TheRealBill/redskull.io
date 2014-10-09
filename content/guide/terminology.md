+++
date = "2014-10-06T19:39:48Z"
title = "RedSkull Terminology"
tags = ["guide"]
type = "post"
description = "RedSkull has a set of terms that differ a bit from standard Redis. This guide walks you through them."
categories = ["documentation","guides"]
weight=4

[menu.guides]
name="RedSkull Terminology"
weight=4

[author] 
name = "Bill Anderson"
email="therealbill@me.com"

+++

# Why New Terms?

What does the phrase "I have a Redis cluster" mean? It means different
things, and any number of them. There is the official Redis Cluster
which is one form of a Redis cluster, there is Redis configured
w/master-slave replication - with or without Sentinel to manage it.

## 'Pod'

In order to help resolve these, at least in my own commnunications I
found I needed soe mname to differentiate the various possible setups.
Somehow I decided to go with "Pod" to identify a Redis master with
replication configured. It seems to have stuck, so until someone comes
up with a cool and better name, Pod it is.

This brings us to Sentinel.

## 'Constellation'

A Pod can be managed by one or more Sentinels, any of all or which
could manage other Pods. Initially I started using Constellation as a
term to identify a group of Sentinels acting together, but with
changes in RedSkull this term now applies to: "The group of Sentinels
managing a specific pod".

# So What does RedSkull Manage?

I'm currently looking for a new and term to apply to the "cluster of
sentinels RedSkull manages". Currently this is listed as Constellation
(for the aforementioned historical reasons), but I'll probably change
it to "Cluster" in the short term while a new name is determined.

Thus, Sentinel manages a cluster of Sentinels from 1 to a number
larger than you should probably use.

At some point in the hopefully near future it will also manage multiple Redis
Cluster errr clusters.

# Action Terms

There are various actions you can take in RedSkull. You might need to "Balance"
a Pod's Constellation, or "Reset" a Pod. This section will guide you through
them.

## Balance

A given pod is assigned a quorum, let us say 2. Redskull will report the pod as
unbalanced if the number of active/configured sentinels is less than quorum+1
sentinels. The act of "Balancing a Pod" is an administrative action which will
attempt to correct this state.

## Resets

Due to the way Sentinel currently manages Pods, there is no way to remove a
sentinel and have the other sentinels known about it. Similarly when Sentinel
detects a node as a slave it will always attempt to ensure the configuration it
had when it was detected will be enforced. This means if you take an existing
slave node and try to move it to a different pod, Sentinel will move it back.

To address these changes the Pod needs 'Reset', which means it issues the
sentinel reset command. This causes the sentinels in the constellation to
forget about the known slaves and sentinels for the pod, and re-detect them,
