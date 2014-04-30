---
layout: post

title: "How to : Realtime Javascript (Easily)"
cover_image: cover.png

excerpt: "Utilisez la puissance de Firebase !"

author:
  name: Rémi Delhaye
  twitter: rdlhio
  gplus: 110592770316565776182
  bio: Étudiant Ingénieur - Rails lover
  image: me.png
---

## Realtime Javascript ?

Le " Javascript en temps réel " est un concept se basant sur la puissance du Javascript et plus particulièrement du [Node.js](http://nodejs.org/).

Node.js nous permet d'utiliser le langage JavaScript sur le serveur... Il nous permet donc de faire **du JavaScript en dehors du navigateur !**
Node.js bénéficie de la puissance de JavaScript pour proposer une toute nouvelle façon de développer des sites web dynamiques.

> Quel rapport avec le temps-réel ?

Et bien grâce à un serveur NodeJS et plus particulièrement à la librairie [Socket.io](http://socket.io/), nous allons pouvoir ouvrir un petit  "canal" entre le serveur NodeJS et votre gentil navigateur afin de pouvoir faire du **"push"** ! Ce qui signifie, envoyer des informations en temps réel *(facile)* et surtout recevoir des données du serveur en temps réel ! *(moins facile)*.

Nous n'aurons pas le temps de voir comment marche NodeJS *(une prochaine fois peut-être ?)* dans ce " How to ", et nous allons donc utiliser un framework proposé par l'entreprise **[Firebase](https://www.firebase.com/)** basée a San Francisco.

Firebase nous propose un framework très simple d'utilisation et nous donne un petit morceau de son énorme serveur NodeJS afin de faire marcher notre app. Cette solution est gratuite sous certaines conditions *(50 Max Connections, 5 GB Data Transfer, 100 MB Data Storage)*. Les données sont stockées sur un de leur serveur [MongoDB](https://www.mongodb.org/), de ce fait, une simple page en `.html` nous permet de faire du Node **sans avoir à rien installer !**

## Un peu de pratique

Fini le blabla, promis !

### Installation

* Inscrivez-vous [sur le site](https://www.firebase.com/account/#/).

* Créez [votre firebase](https://www.firebase.com/account/#/) (en haut à gauche de l'écran).

* Remplacez les `<YOUR-FIREBASE>` qui vont suivre par le nom de votre firebase !

* Allez sur l'url suivante (la aussi remplacez le `<YOUR-FIREBASE>`) afin de voir votre base de donnée de façon graphique ! [https://YOUR-FIREBASE.firebaseio.com/](https://YOUR-FIREBASE.firebaseio.com/)

Comme je vous l'ai dit (pour ceux qui on eu le courage de lire jusqu'à la fin !), ceci est très simple !

Insérez cette ligne dans votre page `.html` :

```html
<script type='text/javascript' src='https://cdn.firebase.com/js/client/1.0.11/firebase.js'></script>

```

Suite à ça, insérez un petit script dans votre page `Javascript` ou `Coffeescript` (qui sera chargée APRÈS le cdn de firebase of course !) :

* Javascript

```javascript
var messagesRef = new Firebase("https://<YOUR-FIREBASE>.firebaseio.com/");

```

* Coffeescript

```coffeescript
messagesRef = new Firebase "https://<YOUR-FIREBASE>.firebaseio.com/"

```

### Insérer des données

Nous allons ajouter des données à la racine de notre base `fb`, pour ça rien de plus simple !

* Javascript

```javascript
messagesRef.push({name:"Rémi Delhaye", text:"Hello World !"});

```

* Coffeescript

```coffeescript
messagesRef.push
  name: "Rémi Delhaye"
  text: "Hello World !"

```

Et voilà, nous venons d'ajouter une donnée avec un identifiant unique (grâce au `.push`) contenant `name` = `Rémi Delhaye` et `text` = `Hello World !` !

## Mettre en place le "temps réel" et récupérer les données

Utilisone le code suivant (que je vais expliquer par la suite) :

* Javascript

```javascript
messagesRef.limit(10).on('child_added', function (snapshot) {
  var message = snapshot.val();
  $('<div/>').text(message.text).prepend($('<em/>').text(message.name+': ')).appendTo($('#messagesDiv'));
    $('#messagesDiv')[0].scrollTop = $('#messagesDiv')[0].scrollHeight;
});

```

* Coffeescript

```coffeescript
messagesRef.limit(10).on "child_added", (snapshot) ->
  message = snapshot.val()
  $("<div/>").text(message.text).prepend($("<em/>").text(message.name + ": ")).appendTo $("#messagesDiv")
  $("#messagesDiv")[0].scrollTop = $("#messagesDiv")[0].scrollHeight
  return


```

Et ajoutez un peu de HTML ainsi que Jquery dans votre page `.html` :

```html
<!-- Dans le <body> -->

<div id='messagesDiv'></div>
<input type='text' id='nameInput' placeholder='Name'>
<input type='text' id='messageInput' placeholder='Message...'>

<script src='https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js'></script>

```

**Explication :**

```javascript
messagesRef.limit(10).on('child_added', function (snapshot) {

});

```

Ce code vas récupérer les 10 derniers (`.limit(10)`) données ajoutées (`.on('child_added'`) dans notre firebase `messageRef` et nous les renvoyer une à une (`snapshot`).

```javascript
var message = snapshot.val();

```

Nous stockons la valeur du `snapshot` dans la variable `message` (rien de bien sorcier).

```javascript
$('<div/>').text(message.text).prepend($('<em/>')
      .text(message.name+': ')).appendTo($('#messagesDiv'));

```

Cette partie crée (grâce à JQuery) une `div` (`$("<div/>")`), y dispose de texte du message reçu (`.text(message.text)`), y ajoute ensuite une balise em (`.prepend($("<em/>")`) au début de notre div et y met le nom de l'auteur du message (`.text(message.name + ": "))`). Suite à ça nous ajoutons cette div créée dans notre div principale (`.appendTo($('#messagesDiv'));`).

```javascript
$('#messagesDiv')[0].scrollTop = $('#messagesDiv')[0].scrollHeight;

```

La dernière partie est pûrement esthétique car elle permet de scroll la div `messageDiv` quand on reçoit un nouveau message.

> Recevoir des messages c'est bien, en envoyer c'est mieux !

C'est vrai, alors disposons ce morceau de code dans notre page javascript / coffeescript, et le tour est joué !

* Javascript

```javascript
$('#messageInput').keypress(function (e) {
  if (e.keyCode == 13) {
    var name = $('#nameInput').val()
    var text = $('#messageInput').val();
    messagesRef.push({name:name, text:text});
    $('#messageInput').val('');
  }
});

```

* Coffeescript

```coffeescript
$("#messageInput").keypress (e) ->
  if e.keyCode is 13
    name = $("#nameInput").val()
    text = $("#messageInput").val()
    messagesRef.push
      name: name
      text: text

    $("#messageInput").val ""
  return

```

Et voilà, nous venons de créer notre premier chat en temps réel !

Je vais mettre [en Annexe](#Annexe) le code de la page complète pour ceux qui auraient quelques soucies !

Si vous voulez voir une application un peu plus complexe, vous pouvez [jetter un oeil ici](https://github.com/rdlh/quizzz/tree/develop/public), créée par moi-même, c'est une petite application de Quizz en push !

## Annexe
```html
<html>
<head>
  <script src="https://cdn.firebase.com/js/client/1.0.11/firebase.js"></script>
  <script src='https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js'></script>
  <link rel="stylesheet" type="text/css" href="https://www.firebase.com/css/example.css">
</head>
<body>
<div id='messagesDiv'></div>
<input type='text' id='nameInput' placeholder='Name'>
<input type='text' id='messageInput' placeholder='Message...'>
<script>

  var messagesRef = new Firebase('https://zckhn284dvr.firebaseio-demo.com/');

  $('#messageInput').keypress(function (e) {
    if (e.keyCode == 13) {
      var name = $('#nameInput').val();
      var text = $('#messageInput').val();
      messagesRef.push({name:name, text:text});
      $('#messageInput').val('');
    }
  });
 message.
  messagesRef.limit(10).on('child_added', function (snapshot) {
    var message = snapshot.val();
    $('<div/>').text(message.text).prepend($('<em/>')
      .text(message.name+': ')).appendTo($('#messagesDiv'));
    $('#messagesDiv')[0].scrollTop = $('#messagesDiv')[0].scrollHeight;
  });
</script>
</body>
</html>

```
