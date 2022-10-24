---
title: "Go: Debuggage avec Delve"
date: 2022-10-24T15:06:05+02:00
draft: true
comment: false
---

Delve est un debugger pour les programmes Go, il est même recommandé par rapport à gdb. Il est écris en Go. Pour plus d'informations, voici [son Github](https://github.com/go-delve/delve)

Pour l'installer:
```sh
go install github.com/go-delve/delve/cmd/dlv@latest
```

Pour lancer le debug il faut utiliser la commande:
```sh
dlv debug 
```

Si il s'agit d'un fichier non lié à un fichier go.mod, il est possible de lancer le debuggage d'un fichier avec la commande:
```sh
dlv debug nomFichier.go
```

Ensuite, pour afficher les commandes disponibles dans delve, il faut utiliser la commande **help**. Pour avoir des informations sur les différentes commandes, il faut utiliser la commande **help commande**.  

Divers liens:
[Github officiel du projet](https://github.com/go-delve/delve/tree/master/Documentation/installation)
[Video tuto](https://www.youtube.com/watch?v=r033vEzL6a4)
[Petit tuto de base](https://opensource.com/article/20/6/debug-go-delve)
[Un autre petit tuto](https://golang.cafe/blog/golang-debugging-with-delve.html)