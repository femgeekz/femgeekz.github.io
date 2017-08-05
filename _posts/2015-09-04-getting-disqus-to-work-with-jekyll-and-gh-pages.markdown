---
layout: post
title: "Getting Disqus to Work With Jekyll and gh-pages"
date: 2015-09-04T00:23:29+05:30
author: Swathi Venkatachala
comments: true
tags:
- disqus
- blog
- jekyll
- gh-pages
- migrate from blogger
- how-to
- tech
---

Since I migrated from Blogger to jekyll based site with gh-pages,
I needed a channel for comments and discussions. I had used 
[Disqus](https://disqus.com/) in my previous blog. So, I thought of using it again for
my new blog.

##Getting Started

Simple steps to get [Disqus](https://disqus.com/) working on jekyll with gh-pages.
Two parts:

1. Disqus and
2. Jekyll

###Disqus

1. Log into [Disqus](https://disqus.com/)
2. Goto `Settings` > `Admin` > on the left side dropdown menu, select `Add a new site`.
3. Give your site name and a `shortname`, which will be your unique Disqus URL.
4. Get the [Universal Code](https://disqus.com/admin/universalcode/) and copy the snippet.


###Jekyll

####Step 1:
Your `gh-pages` may have the following hierarchy:

{% highlight sh %}
about.md
_config.yml
css
feed.xml
_includes
index.html
_layouts
LICENSE
_posts
README.md
_sass
_site
{% endhighlight %}
    
####Step 2:
Create a file `_includes/comments.html` and include the copied content from [Disqus Universal Code](https://disqus.com/admin/universalcode/) in the file as below.

{% highlight js %}
{% if page.comments %}
<!-- Add Disqus comments. -->
<div id="disqus_thread"></div>
<script type="text/javascript">
/* * * CONFIGURATION VARIABLES: EDIT BEFORE PASTING INTO YOUR WEBPAGE * * */
   var disqus_shortname = 'femgeekz';
// var disqus_developer = 1; // Comment out when the site is live

/* * * DON'T EDIT BELOW THIS LINE * * */
(function() {
 var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
 dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
 (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
 })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
<a href="http://disqus.com" class="dsq-brlink">comments powered by <span class="logo-disqus">Disqus</span></a>
{% endif %}
{% endhighlight %}

Please make sure you comment the developer variable to run it live!

####Step 3:
In the `_layouts/default.html` file include the comments file just created as below. Refer [_layouts/default.html](https://github.com/femgeekz/femgeekz.github.io/blob/gh-pages/_layouts/default.html)

####Step 4:
Make sure all the posts within `_posts` have `comments:true` in the frontmatter.

You are now good to go!

Happy discussing! :)