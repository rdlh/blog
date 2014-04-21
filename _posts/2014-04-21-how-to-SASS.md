---
layout: post

title: "How to : SASS"
cover_image: cover.png

excerpt: "Découvrez la puissance du SASS CSS !"

author:
  name: Rémi Delhaye
  twitter: rdlhio
  gplus: 110592770316565776182
  bio: Étudiant Ingénieur - Rails lover
  image: me.png
---

## Introduction

Nous savons tous à quel point faire du CSS peut s'avérer être quelque chose de plutôt ... chiant. Mais savez-vous
que le CSS peut à nouveau être fun ?

Les feuilles de style peuvent être tout aussi grandes et complexes que difficiles à maintenir, et c'est la qu'un " préprocessor " devient utile, car oui, le SASS est un préprocesseur CSS !

> Sass lets you use features that don't exist in CSS yet like variables, nesting, mixins, inheritance and other nifty goodies that make writing CSS fun again.

## Les Variables

Les variables peuvent être très utiles en CSS, comme pour une couleur ou une taille. Celles-ci sont déclarées avec un `$`

```sass
$font-stack:    Helvetica, sans-serif
$primary-color: #333

body
  font: 100% $font-stack
  color: $primary-color

```

## Le Nesting (ou l'emboîtement)

Si vous avez déjà fait du [slim html](http://slim-lang.com/) (que je vous conseil d'ailleurs). Vous êtes familiers avec l'utilisation des tabulations afin de `Nester` (oui, ça rends très moche en français !) pour structurer votre page. En SASS, c'est la même chose !

```sass
nav
  ul
    margin: 0
    padding: 0
    list-style: none

  li
    display: inline-block

  a
    display: block
    padding: 6px 12px
    text-decoration: none

```

Comme vous le voyez, les sélecteurs `ul`, `li` et `a` sont `Nestés` (oui j'invente des adjectifs !) dans le sélécteur `nav`, ce qui nous permet d'organiser d'une façon très visible notre code !

## Les " Partials " et " Import "

Afin de diviser/segmenter votre CSS, vous pouvez les séparer entre plusieurs pages. En nommant vos partiales comme ceci : `_partial.sass`, notre préprocesseur SASS vas savoir que cette feuille de style est une feuille partiel (grâce au `_`). Pour incorporer cette feuille partiel, utilisez simplement un `@import` !

```sass
// _reset.sass

html,
body,
ul,
ol
  margin:  0
  padding: 0

```
```sass
// base.sass

@import reset

body
  font-size: 100% Helvetica, sans-serif
  background-color: #efefef

```

## Les " Mixins "

**Une des plus grandes magie du SASS !**

Depuis le CSS3 et le grand nombre de sélecteurs différents (`-webkit`,`-moz`,`-ms`, etc...), le CSS est devenu un peu pénible pour certaine, mais heureusement pour nous, les mixins du SASS vont GRANDEMENT nous aider !

Prenons l'exemple du `border-radius` :

```sass
=border-radius($radius)
  -webkit-border-radius: $radius
  -moz-border-radius:    $radius
  -ms-border-radius:     $radius
  border-radius:         $radius

.box
  @include border-radius(10px)
  // La syntax " +border-radius(10px) " existe aussi

```

Et voilà ce que ça nous donne en CSS :

```css
.box {
  -webkit-border-radius: 10px;
  -moz-border-radius: 10px;
  -ms-border-radius: 10px;
  border-radius: 10px;
}

```

## Les " Extend "

Une des fonctionnalités les plus utiles du SASS ! La fonction `@extend` nous permet de partager des propriétés CSS entre plusieurs sélecteurs, ce qui permet à notre code de rester très `DRY` et court.

```sass
.message
  border: 1px solid #ccc
  padding: 10px
  color: #333

.success
  @extend .message
  border-color: green

.error
  @extend .message
  border-color: red

.warning
  @extend .message
  border-color: yellow

```

Ce qui va nous générer ce CSS :

```css
.message, .success, .error, .warning {
  border: 1px solid #cccccc;
  padding: 10px;
  color: #333;
}

.success {
  border-color: green;
}

.error {
  border-color: red;
}

.warning {
  border-color: yellow;
}

```

Plutôt sympa non ?

## Les opérateurs mathématiques

Le SASS contiens des opérateurs mathématiques à utiliser dans votre feuille de style (comme par exemple `+`, `-`, `*`, `/`, et `%`). Dans l'exemple suivant, nous allons les utiliser pour calculer la taille d'un `article` et d'un `aside` :

```sass
.container
  width: 100%

article[role="main"]
  float: left
  width: 600px / 960px * 100%

aside[role="complimentary"]
  float: right
  width: 300px / 960px * 100%

```

Et voilà ! Nous venons de créer une grille fluide basée sur 960px ! Le code CSS généré est le suivant :

```css
.container {
  width: 100%;
}

article[role="main"] {
  float: left;
  width: 62.5%;
}

aside[role="complimentary"] {
  float: right;
  width: 31.25%;
}

```

## Et comment je peux utiliser le SASS ?

Le SASS utilise la puissance du `Ruby` pour préprocesser, vous devez donc avoir une version à jour de [ruby](https://www.ruby-lang.org/fr/) et un outil de gestion de version (style [RVM](https://rvm.io/)).

1. `gem install sass`
2. `sass -v` qui doit être quelque chose comme `Sass 3.3.5 (Maptastic Maple)`
3. AMUSEZ-VOUS !

Les solutions suivantes sont aussi possibles :

* Utilisez le [Hipster Template](https://github.com/tiste/hipster_template) créé par mon ami [*Baptiste Lecocq*](http://tiste.io) et propulsé par la puissance de [Grunt](http://gruntjs.com/).

## Crédits

Cet article est très largement inspiré de la documentation officielle de SASS [disponible ici](http://sass-lang.com)
