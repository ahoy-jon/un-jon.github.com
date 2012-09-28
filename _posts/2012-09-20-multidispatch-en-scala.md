---
layout: post
title: "Le Multidispatch en Scala"
description: ""
tagline: C'est possible
category: 
tags: [fr, scala]
---
{% include JB/setup %}

##TL;DR 
Le multidispatch existe vraiment en Scala.


François a écrit un très bon article sur le multidispatch en Java sur le [blog de Xebia](http://blog.xebia.fr/2012/09/19/mais-pourquoi-ny-a-t-il-pas-de-multidispatch-en-java/).

L'idée est de compléter un peu sur la partie Scala. En Scala il y a le partern matching qui permet de déclarer un comportement différent par pattern : 

{% highlight scala %}
def stringFrom(value: Any): String = value match {
  case s:String => "String: " + s
  case i:Int => "Integer: " + i
  case o => "Any : " + o
}
{% endhighlight %}