---
layout: post

title: "How to : CoffeeScript"
cover_image: cover.png

excerpt: "Découvrez la puissance du CoffeeScript !"

author:
  name: Rémi Delhaye
  twitter: rdlhio
  gplus: 110592770316565776182
  bio: Étudiant Ingénieur - Rails lover
  image: me.png
---

> It's just JavaScript

## Introduction

**Le CoffeeScript** est un language qui **compile en Javascript**. Largement inspiré de la syntaxe `Ruby`, cet outil vous permet de développer du JavaScript de façon simple et rapide en utilisant la grande abstraction qu'offre Coffee.

Rien de tel que la pratique pour comprendre comment cette petite merveille fonctionne.

## Les variables

En CoffeeScript, inutile de **déclarer** vos variables, cette déclaration se fait d'elle même lors de la compilation *(Ruby style)*, par exemple :

* CoffeeScript

```javascript
number   = 42
opposite = true

```

* JavaScript

```javascript
var number, opposite;

number = 42;

opposite = true;

```

## Les tests si / sinon

L'écriture de vos tests est aussi beaucoup plus simple quand on utilisent le Coffee. Il nous permet par exemple de placer nos tests en fin de ligne *(Ruby style again)*

* CoffeeScript

```javascript
number = -42 if opposite

```

* JavaScript

```javascript
if (opposite) {
  number = -42;
}

```

* CoffeeScript

```javascript
number = -42 if not opposite

```

* JavaScript

```javascript
if (!opposite) {
  number = -42;
}

```

* CoffeeScript

```javascript
if opposite
  number = -42
else
  if number is 42
    number = 42

```

* JavaScript

```javascript
if (opposite) {
  number = 42;
} else {
  if (number === 42) {
    number = 42;
  }
}

```

> Un **isset** *(PHP)* en JavaScript ?!

* CoffeeScript

```javascript
alert 'foo' if bar?

```

* JavaScript

```javascript
if (typeof bar !== "undefined" && bar !== null) {
  alert('foo');
}

```

## Fonctions

Vous en avez marre de passer des heures à écrire vos fonctions ? Vous voulez pouvoir modifier un paramètre sans passer 10 secondes à chercher ou le mettre ? Jettez un oeil à ce qui suit :

* CoffeeScript

```javascript
square = (x) -> x * x

```

* JavaScript

```javascript
square = function(x) {
  return x * x;
};

```

## Objets

Rien de plus simple en CoffeeScript ! Fini de vous perdre dans vos accolades !

* CoffeeScript

```javascript
math =
  root:   Math.sqrt
  square: square
  cube:   (x) -> x * square x

```

* JavaScript

```javascript
math = {
  root: Math.sqrt,
  square: square,
  cube: function(x) {
    return x * square(x);
  }
};

```

## Jouer un peu avec les tableaux

C'est là que l'on commence à s'ammuser !

* CoffeeScript

```javascript
cubes = (math.cube num for num in list)

```

* JavaScript

```javascript
cubes = (function() {
  var _i, _len, _results;
  _results = [];
  for (_i = 0, _len = list.length; _i < _len; _i++) {
    num = list[_i];
    _results.push(math.cube(num));
  }
  return _results;
})();

```

## Et comment je peux utiliser le CoffeeScript ?

D'après moi, utiliser [**NodeJS**](http://nodejs.org/) et [**npm**](https://www.npmjs.org/) est la solution la plus simple. Lancez un petit `npm install -g coffee-script` (`-g` signifie que vous l'instlallez dans le node_modules général de votre ordinateur, retirez-le si vous voulez l'installer uniquement dans le dossier courant). La documentation est [disponible ici](http://coffeescript.org/#usage) !

Les solutions suivantes sont aussi possibles :

* Utilisez le compilateur [Ruby](https://github.com/josh/ruby-coffee-script).
* Ou utilisez le [Hipster Template](https://github.com/tiste/hipster_template) créé par mon ami [*Baptiste Lecocq*](http://tiste.io) et propulsé par la puissance de [Grunt](http://gruntjs.com/).
