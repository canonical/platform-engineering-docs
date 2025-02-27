# High memory usage

One of the causes of high memory usage is when a user who has joined many rooms
spends some time away from Matrix and, upon returning, has many events to
receive, resulting in numerous /sync endpoint requests.

This document will guide on how to detect if this is the case.

```{note}
All the steps assume that you are connected to the bastion and have access to the Synapse and Synapse database environments.
```

```{warning}
All the IPs and users are fake.
```

## Step 1. Find highest number of accesses

Find the leader unit and log in into the synapse-nginx container.

We are going to check the NGINX log file `access.log` to identify the IPs with the
highest number of accesses during this time.

```{terminal}
kubectl exec -it synapse-1 -c synapse-nginc -- /bin/bash
 cd /var/log/nginx/
 cat access.log |grep '06/Feb/2025:14'|awk '{print $(NF-1)}'|sort -n|uniq -c|sort -k1
```

```{terminal}
   ...
   ...
   1520 "87.120.163.53"
   1600 "872.59.228.245"
   2168 "808.89.251.129"
   3427 "96.141.139.92"
```

The `grep` should filter the moment that you noticed a memory usage peak. The peak
can be checked in the Synapse Grafana dashboard. In this case, it occurred between
14:00 and 15:00 on 06/02/2025.

- `cat access.log`: Prints access.log content to the output.
- `grep '06/Feb/2025:14'`: Filters lines containing `06/Feb/2025:14`.
- `awk '{print $(NF-1)}'`: Gets the second to last field in the line.
- `sort -n`: Sorts by number, this is needed for uniq to work.
- `uniq -c`: Counts occurrences of unique strings.
- `sort -k1`: Sorts by the first field (the count from uniq).

> **Note**: Once we have Loki integration in place, this step will be much easier. :-)

From the command above, the IP `96.141.139.92` performed 3427 requests in one hour.
In the next step

## Step 2. Find if requests are not from a provider

An IP in the NGINX log could represent a provider if it serves multiple users
behind a shared IP address, such as in the case of a proxy or a network address
translation (NAT) setup.

A real user from the internet would typically have a unique IP address assigned
by their internet service provider (ISP), without being shared by multiple users.
In such cases, the IP would correspond to a single individual or device accessing
the service directly, rather than through a proxy or NAT setup.

If all requests from a single IP address have the same user-agent, it might
indicate that the traffic is coming from a single real user, rather than
multiple users behind a provider.

Use the following command now to verify that.

```{terminal}
 cat access.log |grep '06/Feb/2025:14'|grep 96.141.139.92|awk -F'"' '{print $(NF-5)}'|sort|uniq -c
```

```{terminal}
 3427 Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Rambox/2.4.1 Chrome/126.0.6478.234 Safari/537.36
```

- `cat access.log`: Prints access.log content to the output.
- `grep '06/Feb/2025:14'`: Filters lines containing `06/Feb/2025:14`.
- `grep 96.141.139.92`: Filters lines containing `96.141.139.92`.
- `awk -F'"' '{print $(NF-5)}'`: Gets the field corresponding to the User-agent
header.
- `sort`: Sorts lines.
- `uniq -c`: Counts occurrences of unique strings.

From the output, all the 3427 have the same user-agent `Mozilla/5.0...` meaning
that the IP corresponds to a single user and we can use the IP for trying to track
who they are.

## Step 3. Find the user

The IP is not typically registered in the table, or if it is, it contains the
unit's IP since NGINX is in front of Synapse. Therefore, this step will require
some luck and investigation.

Log in into the Synapse database now so we can check all the users that has the
same user-agent found in step 2.

```{terminal}
 juju run postgresql/36 get-password
 juju ssh postgresql/35
 psql -h <postgresql unit IP> -U operator -W synapse
 select distinct user_id from devices where user_agent='Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Rambox/2.4.1 Chrome/126.0.6478.234 Safari/537.36';
```

```{terminal}
          user_id
----------------------------
 @apple:ubuntu.com
 @grape:ubuntu.com
 @melon:ubuntu.com
 @strawberry:ubuntu.com
 @banana:ubuntu.com
```

Now we need to find each of these users corresponds to the IP `96.141.139.92`.

One way to do this is by checking if there are requests adding events to rooms.
To do so, access the NGINX container again as in steps 1 and 2, then filter the
logs using the following command:

```{terminal}
cat access.log |grep '06/Feb/2025:14'|grep 96.141.139.92|grep rooms
```

Content example

```{terminal}
192.168.103.128 - - [06/Feb/2025:14:55:13 +0000] "POST /_matrix/client/v3/rooms/!ioioioioioi%3Aubuntu.com/event/... HTTP/1.1" 200 33 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Rambox/2.4.1 Chrome/126.0.6478.234 Safari/537.36" "96.141.139.92" "https"

```

By this request, we know that the user is member of `!ioioioioioi%3Aubuntu.com` room.

Let's compare the previous list with the members of this room. We can get the
members of the room by running the following query.

```{terminal}
 select distinct user_id from room_memberships where room_id='!ioioioioioi:ubuntu.com' order by user_id;
```

```{terminal}
         user_id
----------------------------
 @banana:ubuntu.com
 @user1:ubuntu.com
 @user2:ubuntu.com
```

The only member from the previous list is `@banana:ubuntu.com`.

Bonus:

Also `@banana:ubuntu.com` is one of the members that joined the biggest number of rooms.

We can check this by running the following query.

```{terminal}
synapse=# select * from user_stats_current order by joined_rooms DESC limit 20;
            user_id            | joined_rooms | completed_delta_stream_id 
```

```{terminal}
-------------------------------+--------------+---------------------------
 @user1:ubuntu.com        |          326 |                   3962503
 ...
 @user2:ubuntu.com            |           85 |                   3940704
 @banana:ubuntu.com           |           84 |                   3865021

```

We can't determine the last time the user `@banana:ubuntu.com` was online before now.
However, based on the number of `/sync` requests, it was likely some time ago.
Given the large number of joined rooms, once the user comes back online, Synapse
has to send a significant number of events, leading to the current high memory
usage as per issue [13901](https://github.com/matrix-org/synapse/issues/13901).

## Solution

Unfortunately there is no solution other than to restart the instance.

There is no action to do this, but is possible to restart Synapse by using Pebble:

```{terminal}
juju ssh --container synapse  synapse/0 pebble restart synapse
```
