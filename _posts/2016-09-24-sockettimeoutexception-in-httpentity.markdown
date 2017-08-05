---
layout: post
title: "SocketTimeoutException in HttpEntity"
date: 2016-09-24
author: Swathi Venkatachala
comments: true
tags:
- HttpEntity
- SocketTimeoutException
- Socket
- API
- java
---


Ever ran into a production issue with SocketTimeoutException or Socket Closed error? Well it can be daunting to
find that culprit. If you faced a similar situation while using HttpEntity, I have found a remedy.

Follow the snippet along.

{% highlight sh %}
httpPost = new HttpPost(url);
httpPost.setEntity(requestEntity);
HttpCoreContext context = new HttpCoreContext();
context.setAttribute(RETRY_COUNT, 0);
context.setAttribute(RETRY_REASON, new ArrayList<StatusLine>());
httpResponse = httpClient.execute(httpPost, context);
// custom ApiExchange class with the some setters
apiExchange.setRequestHeaders(..);
apiExchange.setRequestSent(true);
apiExchange.setResponseStatus(httpResponse.getStatusLine());
…
apiExchange.setResponseHeaders(Arrays.asList(httpResponse.getAllHeaders());
apiExchange.setResponseEntity(httpResponse.getEntity());
// consume it all before its gone
apiExchange.setResponseEntityString(EntityUtils.toString(httpResponse.getEntity()));
…
{% endhighlight %}

The key point here to note is `httpResponse.getEntity()` returns `HttpEntity` which is hooked up with the same
underlying connection. 
So we have to consume the required repsonse content as `EntityUtils.toString(httpResponse.getEntity())` before the `HttpResponse` is closed. Having failed to do that, the connection will reset and will throw `Socket closed Error`.

Happy Debugging! :)

- Swatz

