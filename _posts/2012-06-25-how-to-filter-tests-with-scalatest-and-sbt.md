---
layout: post
title: "How to filter tests with ScalaTest and SBT"
description: ""
category: scala
tags: [scala , sbt , scalatest]
---
{% include JB/setup %}


A quick post to show how to filter test with SBT and ScalaTest.

Filtering in ScalaTest works with [tags](http://www.scalatest.org/user_guide/tagging_your_tests). Then the test can be filtered in the [runner](http://www.scalatest.org/user_guide/using_the_runner#filtering) .


You can add filtering argument in sbt via TestOptions [1] : 

{% highlight scala %}
testOptions in Test += Tests.Argument("-l", "MyTag")
{% endhighlight %}

You can look for a complete example about it [https://github.com/un-jon/psychic-octo-lana](https://github.com/un-jon/psychic-octo-lana) and my related pull-request in banana-rdf  : [https://github.com/w3c/banana-rdf/pull/9](https://github.com/w3c/banana-rdf/pull/9) 

Here is the complete build.sbt 

<script src="https://gist.github.com/2987987.js"> </script>




[1] Reference about it : [SbtTesting](https://github.com/harrah/xsbt/wiki/Testing) 







