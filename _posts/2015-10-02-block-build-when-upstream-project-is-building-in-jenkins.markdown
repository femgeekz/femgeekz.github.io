---
layout: post
title: "Block Build When Upstream Project Is Building In Jenkins"
date: 2015-10-02T02:28:50+05:30
author: Swathi Venkatachala
comments: true
tags:
- jenkins
- upstream
- downstream
- block build
- job chaining
- dependency
- continuous integration
- tech
---
Suppose you have a Job A, let us call this a parent job and you have the case where, you may want to build Job B iff ( if and only if ) the job A builds successfully. Then the usual way of setting this up is in the Job B configuration,
`Build after other projects are built` > `Projects to watch` : `Job A` > `Trigger only if build is stable`

Say the Job A and Job B are triggered by SCM remotely. If this is the case, then you definitely would like to have a finer control. Any push to SCM may, would trigger both the jobs - Job A and Job B, even with the aforementioned setting. To avoid this and handle managing job chains more gracefully, we should use a configuration `Advanced Project Options` > `Block build when upstream project is building` in Job B.

However, this actually doesn't run as purported! [Issue](https://issues.jenkins-ci.org/browse/JENKINS-22800)
- What happens?
SCM trigger takes control over `Block build when upstream project is building` option and as a result what we see is Job A and Job B building at the same time.

To avoid this, what can be done is, set the configuration `Post-build Actions`  > `Build other projects` > `Project to build` in Job A and specify the project name as `Job B`. Select `Trigger only if build is stable`

By doing this, any SCM trigger which may actually be triggering Job A and Job B at the same time, won't do so and Jenkins will take a control on this by building Job A and on build success, Job B from the SCM changes would trigger the build.

Hope this helps!

Happy job chaining! :)
