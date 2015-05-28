---
layout: post
title:  POST-MORTEM Staging CoreOS cluster unable to start units
author: Charles Martinot
date:   2015-05-28 11:33:00
---
Our backends services are running in Docker containers, in a CoreOS cluster. We 
recently moved to use [template units][1] in order to be able to scale easily 
across the cluster. Yesterday, while moving everything to use those new 
template units, we found that we couldn't start or load any more services. The
command would just hang and the containers wouldn't start. `fleetctl list-units`
 wouldn't give any relevant information.

### Description of the issue: 
It was impossible to schedule any unit in the cluster after running `fleetctl 
load template@.service`.

### Effect for the user: 
All staging backend were down.

### Solution found: 
We had to create a new cluster and be careful to run `fleetctl submit` instead 
of `fleetctl load` on template files. We created an issue in [coreos' github][2]
. 

### How to avoid that situation: 
+  Never run `fleetctl load`
+  Never run `fleetctl start` on template units

[1]: https://coreos.com/docs/launching-containers/launching/fleet-example-deployment
[2]: https://github.com/coreos/fleet/issues/1236