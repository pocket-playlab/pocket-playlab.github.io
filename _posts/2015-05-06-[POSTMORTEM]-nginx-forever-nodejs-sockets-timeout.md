---
layout: post
title:  POST-MORTEM Forever restarting actionhero server causes requests to hang
author: Charles Martinot
date:   2015-06-02 14:43:00
---
One of our games is running its backend on node.js (actionhero), with nginx as a
 reverse proxy in the front. Over the last week-end we noticed that sometimes 
the game would go slow until we restart the actionhero workers. We are using the
 [forever][1] package to monitor that our service stays up.

### Description of the issue: 
All requests going to one of our servers wouldn't succeed and would timeout.

### Effect for the user: 
The users couldn't access some functionalities of the game.

### Solution found: 
We found out those messages in nginx error logs : 

    *73633493 connect() to unix:/srv/www/project/shared/sockets/actionhero.sock failed (2: No such file or directory) while connecting to upstream, client: XXX.XXX.XXX.XXX, server: project, request: "POST /<endpoint> HTTP/1.1", upstream: "http://unix:/srv/www/project/shared/sockets/actionhero.sock:/<endpoint>", host: "api.XXXXXXX.com"

These are caused by the following, found in `forever.log` :

    error: Forever detected script exited with code: null
    error: Forever detected script exited with code: null
    info: actionhero >> start
    info: actionhero >> start

Something is sending the application a KILL signal but the server restarts. The 
consequence is that the sockets are deleted but nginx still forwards the requests
 to them. The requests hangs and end up in timeout after 300 seconds. 

A little bit of googling shows there is already a [pull request][2] adressing this issue in 
forever, but it hasn't been merged yet. We decided to apply the fix directly on 
our server in order to solve our issue. 

### How to avoid that situation: 
+  Make sure that no other process is sending KILL signal to the app
+  Replace forever by [pm2][3]

[1]: https://github.com/foreverjs/forever
[2]: https://github.com/foreverjs/forever/pull/702
[3]: http://devo.ps/blog/goodbye-node-forever-hello-pm2/