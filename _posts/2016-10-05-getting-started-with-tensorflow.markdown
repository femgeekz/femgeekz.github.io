---
layout: post
title: "Getting Started With TensorFlow"
date: 2016-10-05T11:22:59+05:30
author: Swathi Venkatachala
comments: true
tags:
- tensorflow
- ML
- python
- introduction
- getting started
- neural networks
- deep learning
---

## Introduction to TensorFlow

Conventionally in programming, we tell programs how to solve a problem. In *Neural Networks*, we tell computers what problems to solve and they find out *how-to* based on observations. The technique to train Neural Networks is *Deep Learning*.


### Tensorflow

> TensorFlow, an open source software library for numerical computation using data flow graphs, is the brain child of 
*Google Brain Team*. 

*TensorFlow - Source : [TensorFlow](https://www.tensorflow.org)*

> A data flow graph is a directed graph with nodes and edges. The nodes in the graph represent mathematical operations, while the graph edges represent the multidimensional data arrays (tensors), which interconnect the nodes.The flow of tensors through the graph is where TensorFlow gets its name.

### Idea 
The basic idea of Tensorflow is to create and manipulate the *computational graph*, mostly the numerical operations, thus taking the advantage of CPUs and GPUs.

Conventionally in the world of application of machine learning and the models, refining of the models mostly happen with trial and error method. Tensorflow has a module called *TensorBoard module* which helps to monitor and display how the algorithms are run under the hood. This enables in creating better models by viewing and measuring.

### Usage
While everything looks interesting, a very fundamental question that comes in the mind of every engineer is - Can I deploy it to Production?
Well, the answer is yes - The team at Google has introduced `Tensorflow Serving` system.

> Tensorflow Serving is a production level system to deploy new algorithms and experiments, while keeping the same server architecture and APIs. 

*Tensorflow Serving - Source : [Serving](https://tensorflow.github.io/serving/)*

The main intent here is, that the developers train their models first using TensorFlow. Then, they use TensorFlow Serving’s APIs to react to input from a client. This is then accomplished by a front-end implementation of gRPC.

> gRPC is a modern open source high performance RPC framework which can efficiently connect services in and across data centers with pluggable support for load balancing, tracing, health checking and authentication.

*gRPC - Source : [gRPC](http://www.grpc.io)*

### Getting Started

To install Tensorflow, lets go with the Virtualenv installation, which will not disturb any existing Python programs on your machine. Follow these [steps](https://www.tensorflow.org/versions/r0.11/get_started/os_setup.html#virtualenv-installation) for installation.

To test if the installation works, lets pick the example from the Tensorflow documentation.

{% highlight sh %}
$ python
...
>>> import tensorflow as tf
>>> hello = tf.constant('Hello, TensorFlow!')
>>> sess = tf.Session()
>>> print(sess.run(hello))
Hello, TensorFlow!
>>> a = tf.constant(10)
>>> b = tf.constant(32)
>>> print(sess.run(a + b))
42
>>>
{% endhighlight %}

As we see in the above snippet, `sess=tf.Session()`, is to create a session to run. All the programs interact with the Tensorflow libraries by creating a session as aforementioned with `Session()`. We can then call `run()` method and the associated computation is run.

The vizualization tool, `TensorBoard`, which helps in comprehensive understanding of graph computing graphically, can be invoked by the following command.

`$ tensorboard --logdir=&lt;trace file&gt;`

Hit the [URL](http://localhost:6006/) to witness the dashboard.

More posts on tensorflow coming up...

Happy Learning and Computing! :)





