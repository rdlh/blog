---
layout: post

title: "How to : Neo4j et Cypher (Partie 1)"
cover_image: cover.png

excerpt: "(Neo4j) –[:IS_A]–> (NoSQL Database)"

author:
  name: Rémi Delhaye
  twitter: rdlhio
  gplus: 110592770316565776182
  bio: Étudiant Ingénieur - Rails lover
  image: me.png
---

## Neo4j ? C'est quoi ?

[Neo4j](http://www.neo4j.org/) est un **base de données en graphe** open-source crée et développée par l'entreprise [Neo Technology](http://neotechnology.com/). Elle stock ses données dans des `nœuds` reliés entre eux par des `relations` typées, contenant (les nœuds et les relations) des propriétés.

![http://assets.neo4j.org/img/propertygraph/graphdb-gve.png](http://assets.neo4j.org/img/propertygraph/graphdb-gve.png)

Neo4j se base sur le principe du NoSQL (**N**ot **o**nly **SQL**). Ce principe de base de données "light" correspond simplement à un ensemble de type `clef` -> `valeur`, parfait pour les données inter-connectées (comme par exemple des données relatives à des relations sociales, des listes de prix, ou même des paniers de site e-commerce).

## Introduction à Cypher

[Cypher](http://www.neo4j.org/tracks/cypher_track_use) est le langage de requête utilisé par Neo4j pour interagir avec la base de données, il ressemble un peu à ça :

```
(Graph) –[:RECORDS_DATA_IN]–> (Nodes) –[:WHICH_HAVE]–> (Properties)

```

Pas très glamour hein ? Ne vous inquiétez pas, nous allons voir comment tout cela fonctionne.

Pour commencer, nous allons aller sur la petite console de test que Neo4j nous met à disposition afin de tous avoir une bonne base de départ, elle est [disponible ici](http://console.neo4j.org/) et je vous invite à y aller tout de suite.

## Créer des nœuds

Je vous entends déjà tous venir avec vos *"C'est toi le nœud"* mais bref, passons. Nous trouvons dans notre belle console une base de données par défaut contenant des données provenant de la trilogie Matrix.

```
CREATE (Neo:Crew { name:'Neo' })

```

Analysons ce début de ligne, d'abord, la commande `CREATE` pour créer votre `nœud`, rien de bien complexe. Suite à ça nous avons du texte contenu dans des `()` qui symbolisent le fait que ces données vont être contenues dans un nœud justement. `Neo:Crew`, alors ça, c'est simplement la désignation de notre nœud, `Neo` vas être le nom de ce nœud dans la base, et `Crew` sa catégorie ou sa `classe`. Avec la commande :

```
CREATE (Neo:Crew)

```

Nous avons donc crée un nœud de classe `Crew` portant le jolie nom de `Neo`.

Comme je vous l'ai dit au part avant, un nœud contiens parfois des propriétés, et ce que cette ligne `{ name:'Neo' }` signifie. Sous la forme d'une chaîne JSON simple, nous attribuons des propriétés à ce nœud, ici, un nom (rien à voir avec le nom du nœud dans ce cas !).

Un peu de partique :

* Cliquez sur `Clear DB` en haut à droite de la console.

```
CREATE (Remi:Student {name: 'Rémi Delhaye', age: 21, sexe: 'male', website: 'rdlh.io'})

```

![/images/neo4j/1.png](/images/neo4j/1.png)

## Créer des relations

Pour créer des relations, il nous faut 2 nœuds. Utilisons notre permier `Remi:Student` et créez le suivant :

```
CREATE (Baptiste:Student {name: 'Baptiste Lecocq', age: 20, sexe: 'male', website: 'tiste.io'})

```

![/images/neo4j/2.png](/images/neo4j/2.png)

Ne sont-ils pas mignons tous les deux ? De mon point de vue, je les trouve un peu éloignés, que diriez-vous de les connecter entre eux ?

* Un petit `Clear DB` plus tard...

```
CREATE (Remi:Student {name: 'Rémi Delhaye', age: 21, sexe: 'male', website: 'rdlh.io'}), (Baptiste:Student {name: 'Baptiste Lecocq', age: 20, sexe: 'male', website: 'tiste.io'})

CREATE (Remi)-[:KNOWS {since: 2011}]->(Baptiste)

```

Et voilà ! Après un peu de magie, nous avons nos deux amis reliés ensembles !

![/images/neo4j/3.png](/images/neo4j/3.png)

**Expliquons tout ça :**

Créer une relation est simple : Un nœud : `(Nœud)`, notre relation : `-->`, un autre nœud : `(AutreNœud)`.

À cette relation, nous pouvons ajouter un petit nom (par ce que oui, les relations peuvent avoir des noms !) comme ceci : `-[:MaRelation]->`, puis y ajouter des données (comme pour les nœuds !), ainsi : `-[:MaRelation {clef: 'valeur'}]->`. Un jeu d'enfant non ?!

> C'est sympa ton truc, mais comment je fais pour les requêter tes nœuds maintenant ?

Patience est mère de toutes les vertus mon ami ! **Nous allons voir ça dans la prochaine partie (Comming soon)!**

