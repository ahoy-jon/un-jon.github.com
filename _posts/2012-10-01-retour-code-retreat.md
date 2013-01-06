---
layout: post
title: "Retour code retreat, création d'algo en FP"
description: ""
category: 
tags: []
---
{% include JB/setup %}


Samedi (29/09), j'ai eu l'occasion de participer à un code retreat chez Xebia. Je ne connaissais pas le concept, j'ai beaucoup apprécié le format. 

L'idée du code retreat est de coder et recoder (en repartant de zéro) en binôme le jeu de la vie toutes les heures avec : 
- 45 minutes d'action,
- 10 minutes de retro 
- et quand même 5 minutes de pause. 

Le jeu de la vie, c'est un peu le truc tout con où en 45 minutes il est difficile de faire une implémentation correcte, il y a toujours un truc qui coince, on se prend les pieds dans le tapis.

Pour ma part, j'avais fait le choix de partir avec mes binômes systématiquement en programmation fonctionnelle. La journée a été l'entrainement sur un kata bien connu : 

 - les types sont des concepts, 
 - les fonctions sont des relations entre les types.


Je vais vous montrer comment on peut faire simplement l'implémentation du jeu de la vie en FP avec Scala et Haskell.

#Les types sont des concepts :

Le jeu de la vie, c'est une "grille" et des cellules vivantes sur la grille. On passe d'un état de la grille à un autre à l'aide des régles autour de la notion de vosinage.

Alors une grille c'est bien gentil, mais c'est un peu une grille toute vide avec 10 pellés/2 gliders qui se balladent, donc je choisis de représenter la grille comme un Set de Cellules vivantes. 

En Scala cela donne ceci 

{% highlight scala %}
object GameOfLife {
  //position sur la grille
  type Cellule = (Int, Int)

  //set de cellule vivante
  type Etat = Set[Cellule]

  //suite infinie des états 
  type Simulation = Stream[Etat]
}
{% endhighlight %}

En Haskell :

{% highlight haskell %}
type Cellule = (Integer,Integer)
type Etat    = [Cellule]
type Simulation  = [Etat]
{% endhighlight %}


#Les fonctions sont des relations entre les types

Maintenant pour passer d'un type à un autre, on va définir des fonctions. 

On a besoin : 

- une fonction pour passer d'un Etat à un autre Etat
- une fonction pour calculer le nombre de voisin qu'à chaque Cellule d'un Etat
- quelques fonctions utilitaires pour découper les relations
- une fonction qui construit une suite infinie d'Etats à partir d'un Etat

En Scala, cela donne :

{% highlight scala %}
object GameOfLife {
   
   
   def nextEtat(etat: Etat):Etat = ???
   
   def voisinage(etat:Etat):Map[Cellule,Int /* on pourait utiliser un Nat ... */] = ???
   
   def voisin(cellule:Cellule) : List[Celulle] = ???
   
   /* une petite fonction/valeur pour définir le motif :
    *   -1 0 1
    *  1 X X X
    *  0 X   X
    * -1 X X X
    */
   lazy val motifVoisinage : List[Celulle] = ???
   
   def simFromEtat(etat:Etat): Simulation = ???


}
{% endhighlight %}

L'implémentation des fonctions n'est pas ce qui est important, la priorité est de définir la "signature" (pour les Javaistes), c'est à dire la relation entre l'entrée de la fonction (domaine) et la sortie de la fonction (codomaine).


#Test et TDD

Les tests sont nécessaires avec les langages fonctionnels statiquement typés, mais prennent un autre sens.
Les relations entre les types fournissent beaucoup de garanties sur le comportement. Une relation qui compile est forcement correcte. Tester une fonction revient à vérifier ce que veut dire la relation.

En Scala : 

{% highlight scala %}






