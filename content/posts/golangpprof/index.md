---
title: "Go: Profiler une app"
date: 2023-10-14T17:53:13+02:00
draft: false
comment: false
---

Un petit article mémo pour le profilage en Go.

## Profilage de base

Dans mon cas, je développe un serveur tcp donc le logiciel reste ouvert indéfiniment, cette solution sera également pour les serveurs http et les autres développement utilisant des boucles infinies.

Voici le code de base:
```go
package main

import (
    "fmt"
	_ "net/http/pprof"
)

func main() {
	go func() {
		log.Println(http.ListenAndServe("localhost:6060", nil))
	}()
    // AJOUTER ICI LE RESTE DE VOTRE CODE
    fmt.Println("hello")
}
```
Explications:  
L'import ```_ "net/http/pprof" ``` force l'import du package pprof http, ensuite il faut lancer un serveur http dans une goroutine pour pouvoir récupérer les informations de la mémoire en temps réel.  
Pour accéder aux données en temps réel, il faut se connecter avec le lien: ```http://localhost:6060/debug/pprof```, le port 6060 est celui par défaut.


## Pour crée des fichiers de profilage

Cette méthode demande l'installation de graphviz.  
Une autre méthode consiste à passer par la création d'un fichier qui contient les informations de profilage et qui peux être lu et chargé plus tard par des outils de profilage. Cette technique demande un arrêt propre du programme ce qui peux être compliqué dans le cas de développement avec des boucles infinie.

Voici le code:
```go
package main

import (
    "fmt"
	"os"
	"runtime/pprof"

)

func main() {

	cpuProfile, _ := os.Create("cpuprofile") // crée un fichier qui contiendra les informations du profilage du CPU
	pprof.StartCPUProfile(cpuProfile) // démarre le profilage
	defer pprof.StopCPUProfile() // arrête le profilage
    // AJOUTER ICI LE RESTE DE VOTRE CODE
    fmt.Println("hello")
}
```

Pour récuperer le résultat du profilage, voici un exemple qui permet de récupérer un fichier pdf du profilage:  
```go tool pprof -pdf fichierbuild.exe cpuprofile > cpuprofile.pdf```


## liens utiles
- https://www.youtube.com/watch?v=nok0aYiGiYA
- https://shghadge.medium.com/how-i-used-pprof-to-investigate-huge-memory-consumption-issues-in-our-go-microservice-f22ff74b72ec
- https://oswanderson.medium.com/generating-and-reading-go-golang-profiles-part-1-db33905decee
- https://go.dev/blog/pprof
- https://pkg.go.dev/net/http/pprof
- https://www.benburwell.com/posts/flame-graphs-for-go-with-pprof/
- https://www.goodwith.tech/blog/go-pprof
- https://jvns.ca/blog/2017/09/24/profiling-go-with-pprof/