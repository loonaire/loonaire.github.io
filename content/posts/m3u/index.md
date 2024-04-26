---
title: "Les fichiers m3u et les voitures Peugeot"
date: 2024-04-24T20:20:00+02:00
draft: false
comment: false
---

Cet article fait suite à mon achat récent d'une peugeot 308. Depuis un bon moment (environ 2018), les véhicules peugeot sont équipés d'une tablette appelée NAV, cette tablette est très bien mais il y a eu une grosse regression par rapport aux tablettes précédentes: elle ne permet pas de lire les fichiers d'une clé usb dossier par dossier, au lieu de cela un mode un peu étrange est choisi, toute l'explication est disponible [sur ce lien](https://www.forum-peugeot.com/Forum/threads/le-fonctionnement-du-nac-pour-lorganisation-des-fichiers-audio.8952/). Ici je ne vais pas détailler ce fonctionnement: je m'en moque, je veux juste charger les musiques par dossier.  

## Les playlists

Le moyen le plus simple de charger les fichiers dans l'ordre est de passer par une playlist.  
Une playlist, c'est quoi?  
Il s'agit d'un fichier .m3u, il permet de charger des fichiers musicaux dans un certains ordre. Il existe un format "standard" et un format étendu, ici le format standard suffira, le format étendu n'est pas supporté par tous les appareils et est compliqué à mettre en place.

## Présentation d'un fichier m3u type

Voici un exemple de base:
```m3u
<chemin vers le fichier 1>.mp3
<chemin vers le fichier 2>.mp3
<chemin vers le fichier 3>.mp3
```
Ici j'utilise des fichiers mp3 mais le flac est aussi supporté.
Au final il s'agit d'un simple fichier qui contient un lien vers des fichiers musicaux.  

Le format étendu est un peu plus complet:
```m3u
#EXTM3U
#EXTINF:<durée en s>, <nom à afficher>
<nom du fichier 1.mp3>
#EXTINF:<durée en s>, <nom à afficher>
<nom du fichier 2.mp3>
```

> Attention sur les chemin de fichier: il est préférable d'utiliser des chemins relatif, ainsi il sera plus facile de déplacer le contenu, si le chemin est absolu il y a des risque de non fonctionnement de la playlist


## Crée un fichier m3u

Au final, une simple ligne de commande permet de tous lister et crée une playlist, dans mon cas, je met le fichier à la racine de la clé usb:

> Attention: cette ligne de commande est à utiliser sur linux et il faut se placer dans le dossier de montage de la clé usb

```sh
find . \( -name "*.mp3" -o -name "*.flac" \) -print > all.m3u
```

Et voila nous avons un fichier de playlist fonctionnel qui lira les musiques dossier par dossier.

> Attention: dans mon cas, il ne doit pas y avoir d'erreur sinon le lecteur de la voiture ne fonctionne pas.

## Les améliorations possible

Je n'ai pas trop le temps pour faire ca mais un m3u étendu semble possible avec des outils qui peuvent lire les tags des fichiers musicaux.

## La vraie solution

Au final, la meilleure solution est... la création d'une playlist via VLC, cette solution est simple et efficace et fonctionne.

## Liens utiles
- [https://docs.fileformat.com/fr/audio/m3u/](https://docs.fileformat.com/fr/audio/m3u/)
- [https://www.linuxtricks.fr/wiki/find-rechercher-et-bien-plus](https://www.linuxtricks.fr/wiki/find-rechercher-et-bien-plus)
- [https://www.forum-peugeot.com/Forum/threads/le-fonctionnement-du-nac-pour-lorganisation-des-fichiers-audio.8952/](https://www.forum-peugeot.com/Forum/threads/le-fonctionnement-du-nac-pour-lorganisation-des-fichiers-audio.8952/)