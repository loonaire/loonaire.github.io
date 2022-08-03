---
title: "Golang: Voir la mémoire utilisée par le programme"
date: 2022-08-03T20:06:58+02:00
draft: false
comment: false
---

Je cherchais un moyen d'afficher en Go la mémoire utilisée par un programme en Go.
La solution se trouve dans la librairie standard avec le package runtime.

Voici un exemple de code (l'exemple parle de lui même):

```golang
package main

import (
	"log"
	"runtime"
	"time"
)

func main() {

	m := &runtime.MemStats{}
	time.Sleep(2 * time.Second)
	log.Println("# goroutines: ", runtime.NumGoroutine())
	runtime.ReadMemStats(m)
	log.Println("Memory Acquired: ", m.Sys)
	log.Println("Memory Acquired (MB) :", float64(m.Sys)/1000000)
	log.Println("Memory Used    : ", m.Alloc)
	log.Println("Memory Used  (Mb)  : ", float64(m.Alloc)/1000000)
	log.Println("# malloc       : ", m.Mallocs)
	log.Println("# free         : ", m.Frees)
	log.Println("GC enabled     : ", m.EnableGC)
	log.Println("# GC           : ", m.NumGC)
	log.Println("Last GC time   : ", m.LastGC)
	log.Println("Next GC        : ", m.NextGC)
	//runtime.GC() // retirer le commentaire pour forcer un passage du garbage collector
    // Si vous souhaite executer ce code en boucle, mettez une boucle for{} enrre les println après le ReadMemStats(m)

}

```
