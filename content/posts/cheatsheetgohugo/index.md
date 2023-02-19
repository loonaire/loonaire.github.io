---
title: "Cheatsheet pour gohugo"
date: 2022-06-26T20:42:44+02:00
draft: false
comment: false
---

Voici un rapide mémo pour l'utilisation de gohugo pour la création et l'ajout de contenu.

## Création d'un site

### Crée un projet

Pour créer un projet avec gohugo:

```sh
hugo new site ./
```

Si le dossier n'est pas vide, il faut utiliser la commande:

```sh
hugo new site ./ --force
```

### Utiliser un thème

Pour ajouter un thème à notre projet, il faut récupérer les fichier d'un thème et le placer dans le dossier themes du projet. Ensuite, il faut modifier le fichier config.toml en ajoutant la ligne theme = "nom-du-theme"

### Ajouter à Github

Pour avoir un site de la forme nomutilisateur.github.io, il faut créer un dépot sur github qui a pour nom nomutilisateur.github.io.
Pour avoir un site accessible via nomutilisateur.github.io/nomsite, il faut crée un dépot avec n'importe quel nom puis activer les github pages.
Création dépot nomutilisateur.github.io ou un dépot avec les github pages, accès via nomutilisateur.github.io/nomprojet

### Générer le site

Pour générer le site , il faut utiliser la commande:

```sh
hugo -D
```

### Tester le site en local

Pour voir le site sur l'ordinateur, il faut utiliser la commande:

```
hugo serve
```

### Automatiser la génération du site via Github

Il est possible d'automatiser la génération du site, pour cela il faut activer les github actions, plus d'informations sont disponible [ici](https://gohugo.io/hosting-and-deployment/hosting-on-github/)

## Création d'articles

### Crée un article

commande:  

```sh
hugo new posts/dossier-avec-nom-du-post/index.md
```

### Publier un article

Dans l'entête passer draft de true à false, par défaut il est à true (il est en mode brouillon)

## Liens utiles

[Article sur l'utilisation de gohugo pour les pages github](https://4bes.nl/2021/08/29/create-a-website-with-hugo-and-github-pages/)

[Organisation d'un site sous gohugo](https://gohugo.io/content-management/organization/)
