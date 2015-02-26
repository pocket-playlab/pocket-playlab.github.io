---
layout: post
title:  Running rspec tests in a Docker container on circleci
author: Charles Martinot
date:   2015-02-25 16:18:13
---
In our continous integration pipeline, we are using [github][1], [circleci][2],
[docker][3] and the [Docker Hub][4]. Here is how it goes: a developer pushes to
github, circleci pulls the repository, builds a container, runs the rspec tests
in that container and then deploys it.

However, when we first ran `docker run myorg/container bundle exec rspec` in 
circleci we faced the following error: 

    Failure/Error: Unable to find matching line from backtrace
    Redis::CannotConnectError:
        Error connecting to Redis on localhost:6379 (ECONNREFUSED)

By default, circleci runs redis (as well as other services) on their boxes. The
reason why we can't reach it is because by default, docker is running
containers in bridged mode, giving our container its own IP. To solve this
issue, just run docker in host only mode: 

    docker run --net=host -e RAILS_ENV=test myorg/container bundle exec rspec

This uses the same interface for the container and the host, thus making all 
services listening on localhost available.

You can find more details about docker networking [here][5].

[1]: https://github.com
[2]: https://circleci.com
[3]: https://docker.com
[4]: https://hub.docker.com
[5]: https://docs.docker.com/articles/networking/
