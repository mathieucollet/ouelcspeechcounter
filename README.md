# Ouelc'home

## Après THIBAUD sur l'ANGLAIS

### | Présentation Jérémy (30 secondes) :
Après cette présentation de notre produit, nous allons prendre 2 ou 3 minutes pour nous présenter.
Le grand, tout fin, juste ici c'est Jérémy. C'est le petit jeune de la bande. Il est originaire de Lannion et est arrivé à My Digital School juste après le lycée. Il est en charge, avec moi, du développement de l'application web de Ouelc'home et il est plus particulièrement responsable de la partie front end du projet. C'est une personne de confiance qui sait aussi s'amuser et profiter de la vie. C'est d'ailleurs pour cette raison qu'il a rejoin le projet.

## Après MARGAUX sur LA GENÈSE

### | Cahier des charges (1 minutes 30 secondes) :
L'objectif de l'application est donc de créer du lien social, du VRAI lien social.
Pour ce faire, nous voulons qu'il y ai une notion d'hôte, que nous appelons des Homeurs, et une notion d'invités, que nous appelons les Comeurs.
Le schéma typique se présente ainsi :
- Un Comeur, un hôte, propose un événement, quelqu'il soit (une soirée, un apéro, un partie de foot).
- Des Homeurs, des invités, se propose à participer à l'événement.
- Le Comeur est libre d'accepter ou non la participation des Homeurs.
- Les transactions avec la monnaie de la plateforme (l'OhZeil), sont enregistrer à l'inscription d'un homeur, et sont validées à la fin de l'événement.
- Après coup, les comeurs ont la possibilités de noté et de commenté l'accueil du Homeur

Pour ce faire, nous organisons la création de l'application en 3 fonctionnalitées principales :
- Créer un événement
- Rechercher un événement
- Participer à un événement

Se sont des majeures, beaucoup de mineurs se greffent autours de celles-ci comme :
- Accepter ou non une participation
- Un système d'alertes par mail et par notification interne pour les inscriptions, les refus etc...
- La gestion de son profil
- ou encore la gestion de son porte OzHeil (portefeuille virtuel)

Dans un premier temps, l'application est proposé dans le périmètre du bassin rennais et uniquement pour les personnes majeures.

Enfin, nous souhaitions une plateforme affranchie de plubicité, et dont le seul apport financié se ferait au moment des transactions entre les utilisateurs et la plateforme. Les utilisateurs achetent de l'OhZeil sans cout supplémentaire. Quand ils veulent transformer l'OhZeil en euros, nous prenons une participation.

### | Le Back (4 minutes):
> 4 slides
> - Logo Laravel
> - Schéma TDD
> - Intégration Continue
> - Documentation
>  

La partie back end de l'application à été construire avec le framework Laravel.
Nous avons construit une API RESTful, entièrement en TDD (pour Test Drivent Development) et entièrement documenté.
La méthode de travail est la suivante :
 - J'écris un test qui décrit un appel vers un premier point de l'API.
 - J'éxécute le test.
 - Je corrige l'erreur retournée par le test.
 - Je réexécute le test.
 - Ainsi de suite jusqu'à ce que le test passe correctement.
 - Je peux ensuite écrire un nouveau test, qui concerne le même point d'API si besoin ou un autre.
L'idée est que je ne créé rien avant mes tests. Ni les tables, ni les modèles, rien.

Cela a plusieurs avantages :
- Ca oblige à structurer la manière de coder.
- Cela permet de commencer en réfléchissant au resultat, et non à la logique.
- On peut ainsi produire du code qui va mener au résultat. Et non tomber sur le résultat de notre code.

Comment ca ce passe concretement :
- Je réfléchi à ce dont le developpeur front à besoin, et sous quel forme.
- Pour une liste d'utilisateurs, il va avoir besoin que l'api lui retourne un tableau de users, qui contient des objets user, avec tel et tel champs.
- J'écrit un test qui pointe vers l'url souhaitée de la liste des users. Et je teste que la réponse ai le bon contenu.
- Au tout début du projet, le test va d'abord me dire qu'il n'ai pas connecté à une base de donnée.
- Je vais donc la créer et connecter mon framework a celle-ci.
- En réexécutant mon test plusieurs fois, il va me dire :
  - qu'il ne trouve pas la table user
  - qu'il ne connais pas l'url que j'ai renseigné
  - puis qu'il ne trouve pas le controller en charge de cette url
  - ni la méthode
  - et ainsi de suite jusqu'à ce que le test trouve une réponse identique à celle souhaité.

Pour donné un ordre d'idée, Ouelc'Home c'est 14 tables, 11 modèles, 104 tests et 455 assertions.

L'autre grand intérêt du travail en TDD, c'est que les tests ainsi créé sont aussi utilisés pour l'intégration continue. Chacun des tests est joué avant tout déploiement, limitant ainsi grandement les erreurs possibles.

Je précisais au début que le back est entièrement documenter. Je ne parle pas seulement du code mais bien de l'API elle même. C'était important que le developpeur front n'est ni à chercher, ni à me demander quels sont les points d'API disponnible, quels sont les paramètres, lesquels sont obligatoires etc... 

En récupérant le projet Ouelc'Home Back, le développeur front à accès, sur l'URL /doc à tous les points d'API, ranger, documenter avec des exemples de réponse en succès et en échec.

## Après MARGAUX sur GESTION DE PROJET

### | Choix techniques (1 minute) :
Coté Design les choix techniques se sont fait naturellement. Margaux connaissait déjà une partie de la suite Adobe. Elle a donc utilisé Illustrator pour la création des SVGs et Adobe XD pour le maquettage.

Pour la partie backend c'est Laravel que nous avons choisi parcequ'il répondais a 3 critère principaux : 
- Nous voulions un framework éprouvé et pérenne dans le temps.
- Il était important que la technologie choisie ai une bonne documentation et une forte communauté
- Mais il fallait aussi que le framwork ne soit pas dans un langage totalement nouveau, pour avoir une monté en compétences plutôt accès sur le framework et moins sur le langage lui même.

Pour le front, nous avons choisi NuxtJS, principalement parcequ'il répondais a deux de nos critères les plus importants:
- Nous voulions un front sans aucun rechargement de page pour fluidifier son utilisation.
- Sans pour autant sacrifier le référencement naturel.
NuxtJs permet cela en créant une application dite SSR de manière grandement simplifié par rapport à d'autre framework.

Nos deux projets sont versionnés avec Git et les dépôts sont stocké sur Github. Nous avions tout deux nos habitudes sur cette plateforme, cela nous a permis de nous concentrer sur la mise en place de l'intégration continue (via ce que Github appel des Actions) sans nous soucier du reste.

Nous avons choisi d'herberger notre site sur la plateforme Heroku. Ce choix à d'abord été guidé par la gratuité de son offre étudiante qui nous a permis d'avoir deux serveurs, ainsi qu'une base de donnée. La simplicité de configuration des serveurs et du déploiement automatique ont fini de nous convaincre.

## Après JÉRÉMY sur APPLICATION MOBILE

### | Le chat (30 secondes) :
Une des premières idées que nous avons eu dès le début du projet, c'était d'implémenter un chat entre le Homeur et les différents Comeur de chaque Ohpla. C'est une feature très importante pour que les utilisateurs puissent déjà faire connaissance avant de se rencontrrer. Il était possible techniquement de le faire dès la v1 mais nous avons du faire des choix et il était important de se concentrer sur les majeurs de l'application.
