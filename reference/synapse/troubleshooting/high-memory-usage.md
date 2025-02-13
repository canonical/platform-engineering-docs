# High memory usage

One of the causes of high memory usage is when a user who has joined many rooms
spends some time away from Matrix and, upon returning, has many events to
receive, resulting in numerous /sync endpoint requests.

This document will guide on how to detect if this is the case.

> **Note** All the steps assume that you are connected to the bastion and have access
to the Synapse and Synapse database environments.

> **Note 1** All the IPs and users are fake.

## Find highest request number

Find the leader unit and log in into the synapse-nginx container.

The grep should filter the moment that you noticed a memory usage peak. This can
be checked in the Synapse Grafana dashboard.

```
kubectl exec -it synapse-1 -c synapse-nginc -- /bin/bash
 tail -n 1000000 access.log |grep '06/Feb/2025:14'|awk '{print $(NF-1)}'|sort -n|uniq -c|sort -k1
   ...
   ...
   1520 "87.120.163.53"
   1600 "872.59.228.245"
   2168 "808.89.251.129"
   3427 "96.141.139.92"
```

## Find if they have same client

We are filtering logs by IP and user agent to ensure the traffic comes from
the same user, not a provider.

```
 tail -n 1000000 access.log |grep '06/Feb/2025:14'|grep 96.141.139.92|awk -F'"' '{print $(NF-5)}'|sort|uniq -c
 3427 Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Rambox/2.4.1 Chrome/126.0.6478.234 Safari/537.36
```

## Find the user

The IP is not registered in the table most of the time, or if it is, it
contains the unit's IP since NGINX is in front of Synapse.

Login into the Synapse database now.

```
 juju run postgresql/36 get-password
 juju ssh postgresql/35
 psql -h <postgresql unit IP> -U operator -W synapse
 select distinct user_id from devices where user_agent='Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Rambox/2.4.1 Chrome/126.0.6478.234 Safari/537.36';
          user_id
----------------------------
 @apple:ubuntu.com
 @grape:ubuntu.com
 @melon:ubuntu.com
 @strawberry:ubuntu.com
 @banana:ubuntu.com
```

One of the request get events from the list extracted in step 1 is `/_matrix/client/v3/rooms/!wJiiHsLipVywuWOyNi%3Aubuntu.com/event/...` so the user is member of `!wJiiHsLipVywuWOyNi%3Aubuntu.com` room.

Let's compare the previous list with the members of this room.

```
 select distinct user_id from room_memberships where room_id='!wJiiHsLipVywuWOyNi:ubuntu.com' order by user_id;
```

The only member from the previous list is @banana:ubuntu.com

Also @banana:ubuntu.com is one of the members that joined the biggest number of rooms.

```
synapse=# select * from user_stats_current order by joined_rooms DESC limit 20;
            user_id            | joined_rooms | completed_delta_stream_id 
-------------------------------+--------------+---------------------------
 @user1:ubuntu.com        |          326 |                   3962503
 ...
 @user2:ubuntu.com            |           85 |                   3940704
 @banana:ubuntu.com           |           84 |                   3865021

```

So, we cant check the last time that the user @banana:ubuntu.com was online
before now but assuming the amount of /sync requests, probably that was some
time ago and per the amount of rooms joined, once the user is back it has a lot
of events to receive, causing the high memory usage now as per issue [13901](https://github.com/matrix-org/synapse/issues/13901).

## Solution

Unfortunately there is no solution other than restart the instance.
