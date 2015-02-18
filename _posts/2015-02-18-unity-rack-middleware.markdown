---
layout: post
title:  Working around Unity's awful http library
author: Omar Khan
date:   2015-02-18
---
Unity has terrible support for http, probably as a result of having to support
so many platforms. You get the [WWW][1] class, which

- only supports GET and POST
- doesn't provide a way to access the response headers or status code
- refuses to show the response body for non-200 responses

This makes it hard to write nice RESTful api backends for unity games. There are
some third-party libraries in the asset store that claim to fix these issues,
but we prefer not to use them because they a) cost money, and b) are closed
source.

As a workaround, we have written a [rack middleware][2] that puts the response
status code, headers, and body inside the body itself if the `X-Unity-Response`
header is included in the request. Along with rack's `MethodOverride`
middleware, this allows us to ignore unity's limitations when developing our
backend services.

[1]: http://docs.unity3d.com/ScriptReference/WWW.html
[2]: https://github.com/pocket-playlab/unity-middleware
