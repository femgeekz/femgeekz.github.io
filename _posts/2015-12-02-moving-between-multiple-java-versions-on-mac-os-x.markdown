---
layout: post
title: "Moving Between Multiple Java Versions on Mac OS X"
date: 2015-12-02T01:28:58+05:30
author: Swathi Venkatachala
comments: true
tags:
- java8
- java7
- java
- switch
- versions
- Mac OS X
---

Often, I used to encounter a scenario, where I had to switch Java versions on a need basis.
I did not know how to do this on Mac OS X(x64).

I had installed Java8 and Java7 on my Mac and had to switch to one of them, once in a while.

`/usr/libexec/java_home` gives the top Java Version.
`/usr/libexec/java_home -V` gives the list of JVMs.

 You may give the version of interest by using
`/usr/libexec/java_home -v 1.7`

So, on `.bash_profile` for Java 7, use the following:

{% highlight sh %}
export JAVA_HOME=$(/usr/libexec/java_home -v 1.7)
{% endhighlight %}

Happy Switching! :)
