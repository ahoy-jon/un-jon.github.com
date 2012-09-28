---
layout: post
title: "Multimodule build with Play! 2"
description: ""
category: scala
tags: [play , sbt , scala]
---
{% include JB/setup %}

```TL;DR :``` You can look for a complete working example here : [play2MultiModule](https://github.com/un-jon/play2MultiModule)



Strangely, there are still few people asking about multimodule builds in Play! 2. I think this will change as the framework grow more popular.

Imagine you have a seperate model you want to use in a play app, and a client app, we can organize our project like this : 

<pre>
myAwesomeProject
  - model
  - clientapp
  - playapp
</pre>

You cannot have a common build between this three artefact. Play! 2 is based on sbt, but needs a specific launcher, some options and its own ivy repository.

But you can still have a multimodule build for you ```model``` and ```client```
 artefacts.

The main problem is the definition of the Play! 2 build, you can define a module in the build, but it has to be in the playapp directory (Symlinks ?? ).

A solution to this problem is to use a source dependency in SBT. SBT is recursive by nature, so it will propagate the model's dependencies and settings  : 

<script src="https://gist.github.com/2996520.js"> </script>


Hope this helps !