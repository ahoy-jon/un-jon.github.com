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

C'est une méthode élégante (le pattern matching en Scala poutre sérieusement), mais qui n'a pas l'avantage d'être très extensible. Dans le code précedent, les types traités sont seulement String, Int et Any, si l'on veut traiter MonTypeAMoi, en l'état, ce n'est pas vraiment possible.

Pour faire du vrai multidispath en Scala, on peut utiliser les typeclasses : 

{% highlight scala %}
trait Stringfier[T] {
  def stringFrom(t: T):String
}

implicit val intS = new Stringfier[Int] {def stringFrom(i:Int) = "Int : " + i}
implicit val strS = new Stringfier[String] {def stringFrom(s:String) = "String : " + s }
implicit val objS = new Stringfier[Any] {def stringFrom(a:Any) = "Any : " + a }

def genericStringFrom[T : Stringfier](t :T ) = implicitly[Stringfier[T]].stringFrom(t)


println(genericStringFrom(1))

println(genericStringFrom("abc"))
//ne va pas compiler
//println(genericStringFrom((1,2)))

println(genericStringFrom((1,2).asInstanceOf[Any]))
{% endhighlight %}

Si je veux étendre le comportement de Stringfier je peux écrire : 

{% highlight scala %}
implicit val perS = new Stringfier[Personne] {def stringFrom(p: Personne) = "Personne : " p.nom}
{% endhighlight %}

Et faire l'appel genericStringFrom sur des personnes avec la typeclasse (ie, l'implicit) dans le scope. 

