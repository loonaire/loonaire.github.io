---
title: "Manjaro xfce: Erreur de la variable LANG de locale"
date: 2022-09-22T20:09:00+02:00
draft: false
comment: false
---

Un des obstable à mon éventuel passage de Windows 10 à Manjaro XFCE est le support de certains logiciels. J'avais une demande particulière qui était qu'il fallais que Flash Player 9 StandAlone fonctionne sur Linux en français. Problème: le lecteur démarrais en anglais.  

Après test sur Manjaro KDE ainsi que Mint et Debian XFCE, le problème semblais venir précisement de Manjaro avec l'environnement XFCE.  

Sur Manjaro XFCE, la valeur par défaut de la variable d'environnement LANG est fr_FR.utf8 (obtenu avec la commande **locale**). Cet encodage est inconnu de Flash Player 9 mais est compatible avec les version à partir de Flash Player 10 (précisément  la version 10.1 ou 10.2). Une solution temporaire consiste à changer la valeur de la variable LANG dans un terminal puis d'éxecuter le lecteur Flash Player depuis ce terminal, ces manipulations peuvent se faire en une ligne de commande:

```shell
LANG=fr_FR.UTF-8;./flashplayer
```

Cette commande est plainement fonctionnelle mais pose un problème: l'encodage dans l'application est mal prise en charge. Il faut donc utiliser une autre valeur pour la variable LANG, dans mon cas, la valeur fr affichais correctement les caractères français.

```shell
LANG=fr;./flashplayer
```

A partir de la, je pouvais avoir Flash player en Français, mais le problème ne semble pas venir spécifiquement du Logiciel ni de la valeur definie dans locale mais des paramètres de Manjaro.  

Au final, le problème se corrige en allant dans le Gestionnaires des paramètres Manjaro puis dans Paramètres Régionaux. Il faut ensuite ajouter la Langue Français **fr_FR**  puis la mettre en langue par défaut puis Appliquer. A partir de ce moment, il est possible de remettre la valeur de la langue en **fr_FR.UTF-8** par défaut. Enfin, il faut redémarrer l'ordinateur puis flash player 9 s'affichera en français avec un support correct des caractères spéciaux (et donc mieux que sur les autres distributions linux!). 

Je n'ai pas chercher plus mais j'imagine que cela peux corriger d'autres problèmes de traductions sur d'autres logiciels.
