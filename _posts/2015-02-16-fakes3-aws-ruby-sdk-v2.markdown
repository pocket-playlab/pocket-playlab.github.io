---
layout: post
title:  Using FakeS3 with V2 of the AWS SDK for Ruby
author: Omar Khan
date:   2015-02-16 15:10:13
---
AWS [recently launched version 2 of its ruby client library][1]. I like to use
[FakeS3][2] to test code that uses S3, to avoid complicated mocking of the
various api calls involved. With the old ruby client library it was easy enough
to pass in the fake S3 endpoint and have everything just work. It's still
possible with the new sdk, but the options are slightly different:

{% highlight ruby %}
# fakes3 --port 10001 --root /tmp/s3
Aws::S3::Client.new(
  access_key_id: 'key',
  secret_access_key: 'secret',
  region: 'region',
  endpoint: 'http://localhost:10001/',
  force_path_style: true
)
{% endhighlight %}

Hope that saves somebody a few minutes of digging around the sdk docs.

[1]: http://ruby.awsblog.com/post/Tx2NJE86FP0HHXX/Announcing-V2-of-the-AWS-SDK-for-Ruby
[2]: https://github.com/jubos/fake-s3
