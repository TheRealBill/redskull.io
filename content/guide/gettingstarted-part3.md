+++
date = "2014-10-13T14:29:40Z"
title = "Getting Started Pt. 3"
tags=["API","JSON","intro"]
weight = 3

[menu.guides]
	name = "Getting Started Pt.3"
	weight = 3


[Params]
	subtitle = "Using the HTTP-JSON API"


[author]
  email = "therealbill@me.com"
  name = "Bill Anderson"

+++


# Using the HTTP+JSON API

Red Skull offers an HTTP based, REST-like API with JSON for managing your pods
and cluster as well as for pulling information about your cluster and it's
managed resources. This provides an easy path for better "stack-up"
monitoring integration.

For example, rather than query each node and run the checks yourself you
can ask Red Skull. The exposure of an API via HTTP+JSON should integrate
well with most open source monitoring systems.

*Currently the API is in flux.* In particular the structure of the
returned JSON is undergoing development. It will do so until Release 1.0
when it will be stabilized. If you use the API you should join the Red
Skull Google group to keep abreast of changes as well as provide use cases
and other input into the API.

Currently the API will return a hash structure of the following format:

```
{"Status":"STATUS","StatusMessage":A Status message here","Data":null or a
data structure}
```

The type of structure found in data depends on the call. In many cases it
will simply be null.

# Task: Monitor a Pod

As there is not a lot you can do without monitoring a pod, we'll first
create a monitored pod. To add a pod you will need enough sentinels to
form a quorum. Currently the API doesn't expose adding sentinels, but
should do so soon. Thus for this exercise use the Web UI to add two
additional sentinels if using a quorum of two and you aren't interested in
spreading pods across a bank of sentinels. Of course, feel free to create
as many sentinels as you feel you need or want.

To add a Pod Red Skull needs to know the Name IP, Port, quorum, and any authentication
information needed to access the master.  The simple, and recommended
route is to issue an HTTP PUT against `/api/pod/NAME' with the
authentication and quorum information in a JSON body.

```
curl -X PUT -H "Accept: application/json" -H "Content-Type: \
application/json" -d '{"Quorum":2,"AuthToken":"my-secret-password", \
"MasterAddress": "127.0.0.1", "MasterPort": 6379}' \
http://localhost:8000/api/pod/pod1
```

The above example assumes a Quorum of 2 and that the `requirepass`
directive for the Redis master running at 127.0.0.1 on port 6379 is set to
'my-secret-password'. It will name the pod 'pod1'

If You don't have enough Sentinels, you will get a response like the
following:

```json
{"Status":"INCOMPLETE","StatusMessage":"Pod '127.0.0.1:6379l' failed to
reach sentinel quorum.","Data":null}
```

On a successful add you'll get the following back:
```json
{"Status":"COMPLETE","StatusMessage":"","Data":null}
```

# Task: Get Pod Info

The next call we'll be learning is how to get the current list of
monitored pods. As with all examples in the GSG we will be using Curl.

```
curl http://localhost:8000/getknownpods
```

If you have followed the above steps you should see the following output
(formatted for ease of reading):

```json
{
    "Data": [
        {
            "ActiveSentinelCount": 0,
            "AuthToken": "my-secret-password",
            "HasInfo": false,
            "HasValidSlaves": false,
            "Info": {
                "ConfigEpoch": 0,
                "DownAfterMilliseconds": 30000,
                "FailoverTimeout": 180000,
                "Flags": "master",
                "IP": "127.0.0.1",
                "InfoRefresh": 1301,
                "IsMasterDown": false,
                "LastOkPingReply": 983,
                "LastPingReply": 983,
                "LastPingSent": 0,
                "Name": "pod1",
                "NumOtherSentinels": 2,
                "NumSlaves": 1,
                "ParallelSyncs": 1,
                "Port": 6501,
                "Quorum": 2,
                "RoleReported": "master",
                "RoleReportedTime": 770697,
                "Runid": "f7e795d7aaaae1fd36380d18873f352cf664ad45"
            },
            "Master": {
                "AOFEnabled": false,
                "Address": "127.0.0.1",
                "Auth": "my-secret-password",
                "HasEnoughMemoryForMaster": false,
                "Info": {
                    "CPU": {
                        "UsedCPUChilden": 0,
                        "UsedCPUSystem": 798.06,
                        "UsedCPUUser": 585.22,
                        "UsedCPUUserChildren": 0
                    },
                    "Client": {
                        "BlockedClients": 0,
                        "ClientBiggestInputBuffer": 0,
                        "ClientLongestOutputList": 0,
                        "ConnectedClients": 0
                    },
                    "Commandstats": {
                        "Stats": {
                            "auth": {
                                "calls": 20173,
                                "usec": 183912,
                                "usec_per_call": 9.12
                            },
                            "client": {
                                "calls": 38,
                                "usec": 56500,
                                "usec_per_call": 1486.84
                            },
                            "config": {
                                "calls": 1947,
                                "usec": 27336,
                                "usec_per_call": 14.04
                            },
                            "info": {
                                "calls": 159697,
                                "usec": 66557313.0,
                                "usec_per_call": 416.77
                            },
                            "ping": {
                                "calls": 1522777.0,
                                "usec": 10242897.0,
                                "usec_per_call": 6.73
                            },
                            "psync": {
                                "calls": 1,
                                "usec": 1547,
                                "usec_per_call": 1547
                                "usec_per_call": 1547
                            },
                            "publish": {
                                "calls": 766966,
                                "usec": 19621624.0,
                                "usec_per_call": 25.58
                            },
                            "replconf": {
                                "calls": 1141038.0,
                                "usec": 19758895.0,
                                "usec_per_call": 17.32
                            },
                            "subscribe": {
                                "calls": 26,
                                "usec": 97096,
                                "usec_per_call": 3734.46
                            }
                        }
                    },
                    "Keyspace": {
                        "Databases": null
                    },
                    "Memory": {
                        "MemoryAllocator": "jemalloc-3.6.0",
                        "MemoryFragmentationRatio": 1.26,
                        "UsedMemory": 2156968,
                        "UsedMemoryHuman": "2.06M",
                        "UsedMemoryLua": 33792,
                        "UsedMemoryPeak": 2618072,
                        "UsedMemoryPeakHuman": "2.50M",
                        "UsedMemoryRss": 2707456
                    },
                    "Persistence": {
                        "AOFEnabled": false,
                        "BGSavesInProgress": false,
                        "ChangesSinceSave": 0,
                        "CurrentBGSaveTime": 0,
                        "CurrentRewriteTimeInSeconds": -1,
                        "FRewriteInProgress": false,
                        "LastAOFWriteSTats": "ok",
                        "LastBGRewriteStatus": "ok",
                        "LastBGSaveStatus": "ok",
                        "LastBGSaveTime": 0,
                        "LastRewriteTimeInSeconds": -1,
                        "LastSaveTime": 0,
                        "Loading": false,
                        "RewriteScheduled": false
                    },
                    "Replication": {
                        "ConnectedSlaves": 1,
                        "MasterHost": "",
                        "MasterLastIOSecondsAgo": 0,
                        "MasterLinkStatus": "",
                        "MasterPort": 0,
                        "MasterReplicationOffset": 100517117,
                        "MasterSyncInProgress": false,
                        "ReplicationBacklogActive": 1,
                        "ReplicationBacklogFirstByte": 99468542,
                        "ReplicationBacklogHistLen": 1048576,
                        "ReplicationBacklogSize": 1048576,
                        "Role": "master",
                        "SlavePriority": 0,
                        "SlaveReadOnly": false,
                        "SlaveReplicationOffset": 0,
                        "Slaves": [
                            {
                                "IP": "127.0.0.1",
                                "Lag": 0,
                                "Offset": 100516988,
                                "Port": 6505,
                                "State": "online"
                            }
                        ]
                    },
                    "Server": {
                        "Arch_bits": 64,
                        "ConfigFile": "",
                        "GCC_version": "4.8.2",
                        "Git_dirty": false,
                        "Git_sha1": 0,
                        "Hz": 10,
                        "LRU_clock": 3928393,
                        "Mode": "standalone",
                        "OS": "Linux 3.13.0-24-generic x86_64",
                        "ProcessId": 23411,
                        "TCPPort": 6501,
                        "UptimeInDays": 13,
                        "UptimeInSeconds": 1144560,
                        "Version": "2.8.14"
                    },
                    "Stats": {
                        "EvictedKeys": 0,
                        "ExpiredKeys": 0,
                        "InstanteousOpsPerSecond": 7,
                        "KeyspaceHits": 0,
                        "KeyspaceMisses": 0,
                        "LatestForkUsec": 930,
                        "PubSubChannels": 1,
                        "PubSubPatterns": 0,
                        "RejectedConnections": 0,
                        "SyncFill": 1,
                        "SyncPartialErr": 0,
                        "SyncPartialOk": 0,
                        "TotalCommandsProcessed": 3612663,
                        "TotalConnectionsRecevied": 28209
                    }
                },
                "LastStart": "2014-09-30T09:39:37.512936075Z",
                "LastUpdate": "2014-10-13T15:35:37.513347851Z",
                "LastUpdateDelay": 452834,
                "LastUpdateValid": true,
                "MaxMemory": 5000000,
                "MemoryUseCritical": false,
                "MemoryUseWarn": false,
                "Name": "127.0.0.1:6501",
                "PercentUsed": 43.139359999999996,
                "Port": 6501,
                "SaveEnabled": false,
                "Slaves": null
            },
            "MissingSentinels": false,
            "Name": "pod1",
            "NeededSentinels": 0,
            "NeedsReset": false,
            "ReportedSentinelCount": 0,
            "SentinelCount": 3,
            "Slaves": null,
            "TooManySentinels": false,
            "ValidAuth": false
        }
    ],
    "Status": "COMPLETE",
    "StatusMessage": ""
}

```

As you can see, there is quite a bit of information in that call for a
single pod. The data structure is a list of dictionaries. For each pod you
essentially get the current known state of each pod. For those familiar
with Sentiel's API you may recognize this as the same data as output by
the `SENTINEL SENTINEL <podname>` command, with some additions by Red
Skull. Specifically it includes Red Skull audit results.

Contained in this output you will see, for example, 'MissingSentinels' is
false which means it has all of the needed sentinels. You will note,
however there are no slaves attached to the master. Yes, Red Skull reports even
the info command results from the master. It can "get away with it" because it
already has that information in order to perform it's auditing and status
checking. I figure it may as well return it and potentially save you some
calls.


# Task: Get Info For Specific Pod

While the `/knownpods` call will give you pod-level information it does it
for all pods. This may not be always desired. Sometimes you want to just
report or check on a specific pod. An example of this is in monitoring.

The same information but specific to a pod is obtained via
`http://localhost:8000/api/pod/PODNAME`.

In this call you'll see a couple entries you probably want to monitor for:
'MemoryUseCritical' and 'MemoryUseWarn'. This are currently set to 60 and
80% of the value of `maxmemory`. This allows you to monitor your memory
consumption.

A simple command-line check might look something like this:

```shell
curl http://localhost:8000/api/pod/pod1 |grep '"MemoryUseCritical": true'\
 && echo "OH NO, MEMORY USE IS HIGH" 
```

# Task: Get Current Master

Sometimes you don't want to parse the entire information structure just to
figure out what the current master is. With that in mind:

```shell
curl http://localhost:8000/api/pod/pod1/master
```

Will return just that information:
```json
{"Status":"SUCCESS","StatusMessage":"","Data":{"Host":"127.0.0.1","Port":6501}}
```

# Task: Get Current Slave List

Sometimes you don't want to parse the entire information structure just to
figure out what the current slave list is. With that in mind:

```shell
curl http://localhost:8000/api/pod/pod1/slaves
```

Will return just that information:

```json
{
    "Data": [
        {
            "Flags": "s_down,slave,disconnected",
            "Host": "127.0.0.1",
            "InfoRefresh": 1413215608423,
            "IsMasterDown": false,
            "LastOkPingReply": 1403244,
            "LastPingReply": 13027,
            "LastPingSent": 1403244,
            "MasterHost": "?",
            "MasterLinkDownTime": 0,
            "MasterLinkStatus": "err",
            "MasterPort": 0,
            "Name": "127.0.0.1:6505",
            "PendingCommands": -2,
            "Port": 6505,
            "RoleReported": "slave",
            "RoleReportedTime": 1403244,
            "Runid": "",
            "SlavePriority": 100,
            "SlaveReplicationOffset": 0
        }
    ],
    "Status": "",
    "StatusMessage": ""
}
```

In this case we have a single slave running at 127.0.0.1 on port 6505 who
is having problems connecting to his master.



# Task: Add Slave to Master

You can use the API to add a slave to a master already managed by Red
Skull. This is a pod level operation so the URL is:
`http://localhost:8000/api/pod/pod1/addslave`.

Here is an example Curl call:
```shell
curl -X PUT -H "Accept: application/json" -H "Content-Type: \
application/json" -d '{"SlaveAddress":"127.0.0.1", "SlavePort":6505,\
"SlaveAuth":"pass1"}' \
http://localhost:8000/api/pod/pod1/addslave
```

It is important to note the `SlaveAuth` should match any *existing*
`requirepass` directive on the slave. Red Skull will use that to connect,
but will change it to the current password for the pod to ensure proper
functioning.


# Conclusion

Here we have the basic commands available via the API. More pod-level
actions are coming I've just not yet exposed them in the API. However,
with the current supported calls you can easily integrate Red Skull into a
monitoring system as well as programmatically add pods and slaves. With a
provisioning system which deploys running Redis instances you can build
Pods as you need them in ways you need them,
