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


##Exemple depuis lisp

L'exemple "classique" du multidispatch, c'est la gestion de collision entre plusieurs objets en Lisp : 

{% highlight lisp %}
(defmethod collide-with ((x asteroid) (y asteroid))
   ;; deal with asteroid hitting asteroid
   )
 (defmethod collide-with ((x asteroid) (y spaceship))
   ;; deal with asteroid hitting spaceship
   )
 (defmethod collide-with ((x spaceship) (y asteroid))
   ;; deal with spaceship hitting asteroid
   )
 (defmethod collide-with ((x spaceship) (y spaceship))
   ;; deal with spaceship hitting spaceship
   )
 {% endhighlight %}

 En Scala cela donne avec l'implémentation :

{% highlight scala %}
  case class CollidingResult(result:String)
  
  trait Collider[A,B] {
      def collideWith(a:A,b:B): CollidingResult
  }

  case class Asteroid(name:String)

  object Asteroid {
    implicit val collide = new Collider[Asteroid,Asteroid] {
      def collideWith(a1:Asteroid,a2:Asteroid) = CollidingResult("un paquet de petits cailloux.")
    }
  }

  case class Spaceship(name:String)
  object Spaceship {
    implicit val collide = new Collider[Spaceship,Spaceship] {
      def collideWith(s1:Spaceship,s2:Spaceship) = CollidingResult(s1.name + " et " + s2.name + " forment maintenant un paquet de metal coupant.")
    }

  
  }

  object CollidingRules{

    def collideSandA(s:Spaceship,a:Asteroid) = CollidingResult(s.name + " a participé avec l'asteroïd " +
       a.name + " à la formation d'une amas de débris.")

    implicit val collideWithAst = new Collider[Spaceship,Asteroid] {
      def collideWith(s:Spaceship,a:Asteroid) = collideSandA(s,a)

      
    }
    implicit val collideWithSp  = new Collider[Asteroid,Spaceship] {
      def collideWith(a:Asteroid,s:Spaceship)  = collideSandA(s,a)
    
    }
  }
  
  object Collider{
    def apply[A,B](a:A,b:B)(implicit c : Collider[A,B]) = {
      import c._

      println(collideWith(a,b))
    }
  }

  val aa = new Asteroid("A")
  val ab = new Asteroid("B")
  
  //la résolution de l'implicit se faire par l'objet companion d'Asteroid
  Collider(aa,ab)
  val ahoy = Spaceship("Ahoy !")
  val dermat = Spaceship("dermat !")

  Collider(ahoy,dermat)

  // ne compilera pas, l'implicit n'est pas dans le scope 
  //Collider(aa,ahoy)
  
  import CollidingRules._
  Collider(aa,ahoy)
{% endhighlight %}

S'il on veut étendre les régles de la simulation, on peut faire comme ceci : 

{% highlight scala %}
case class NX(name:String)
  object NXCollidingRules {
    implicit val collideWithAst = new Collider[NX,Asteroid] {
      def collideWith(n:NX,a:Asteroid) = CollidingResult(n.name + """ dit : "même pas mal" .""") 
    }  

    implicit val collideWithSp = new Collider[NX,Spaceship] {
      def collideWith(n:NX,s:Spaceship) = CollidingResult(n.name + "(classe NX) vient d'enlever un gros bout au spaceship " + s.name + "." )
    }
    // ...... //
  }

  import NXCollidingRules._

  val entreprise = NX("Entreprise")

  //Fiction
  Collider(entreprise,aa)
  Collider(entreprise,ahoy)

{% endhighlight %}

##Conclusion 

On a le multidispatch en Scala via les typeclasses. C'est un peu verbeux, mais c'est typesafe. Cela permet de faire des conceptions très sympathiques.



##Une note sur les typeclasses

Le concept de typeclasses en Scala est hyper important, je ne vais pas le couvrir ici, je vous recommande d'aller regarder [la présentation de Seth Tissue sur le sujet](http://marakana.com/s/the_typeclass_pattern_in_scala_an_alternative_to_inheritance,1112/index.html).

On peut aussi voir du coté de Scalaz qui utilise complétement ce concept.





