---
layout: post
title:  POST-MORTEM Production services downtime after a deployement
author: Charles Martinot
date:   2015-05-06 16:52:00
---
From now on, we will publish our post-mortems here. We hope that what we learn 
from fixing downtimes and incidents will help others in turn.

If you're not doing post-mortems yet, you should. Here is a [good article][1] 
explaining why and how to do them.

### Description of the issue : 
The storage service and backends for all games were unreachable for between 5 
and 30 minutes

### Effect for the user : 
All transactions with the backend were unable to be 
processed (in-app purchases, loading and saving data...)

### Solution found : 
An environment variable was missing for one of our unused 
backend. This variable is used to dynamically reconfigure our nginx-proxy 
container, running on all servers. Its absence was leading to our config having 
that block : 

    upstream  {
       server XXX.XXX.XXX.XXX:XXXX;
    }

`upstream` requiring one argument, the reloading of the configuration was 
failing, and thus all services running on that server were unreachable. We are 
using a [fork][2] of [jwilder/nginx-proxy][3], so if you don't set your 
VIRTUAL_ENV variable for one of your containers exposing a port, you might run 
into the same issue. Just set a dummy variable if need be.

### How to avoid that situation : 
+  Always set required environment variables before running a container
+  Make the container fail if the required environment variables are missing
+  Return errors during the deployment process and notify the team in chat

[1]: https://codeascraft.com/2012/05/22/blameless-postmortems/
[2]: https://registry.hub.docker.com/u/pocketplaylab/nginx-proxy/
[3]: https://registry.hub.docker.com/u/jwilder/nginx-proxy/