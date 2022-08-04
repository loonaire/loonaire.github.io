---
title: "Passer des arguments à un programme Go avec les flags"
date: 2022-08-04T14:08:36+02:00
draft: false
comment: false
---

Go permet de se passer de Os.args pour la gestion des arguments à l'execution de l'application avec le package flag.

Les arguments créeront l'argument -h ou --help.

Pour les arguments de type bool, il faut mettre par défaut la valeur false, sinon l'argument sera toujours à true.

Voici un exemple de base, chaque argument est envoyé dans un pointeur vers l'argument:

```golang
package main

import (
	"flag"
	"log"
)

func main() {
	// crée les variables qui contiendront les arguments
	// attention: chaque paramètre est précédé d'un -
	boolArg := flag.Bool("b", false, "Argument de type booléen")
	stringArg := flag.String("s", "", "Argument de type chaine de caractères")
	intArg := flag.Int("i", 0, "Argument de type entier signé")
	uintArg := flag.Uint("u", 0, "Argument de type entier non signé")
	floatArg := flag.Float64("f", 0.0, "Argument de type nombre décimal")

	// parse les arguments saisis
	flag.Parse()

	//flag.PrintDefaults() // permet d'afficher le message d'aide, équivaut à ./programme --help ou ./programme -h

	log.Println("Nombre d'arguments: ", len(flag.Args()))
	log.Println("boolArg value: ", *boolArg)
	log.Println("stringArg value: ", *stringArg)
	log.Println("intArg value: ", *intArg)
	log.Println("uintArg value: ", *uintArg)
	log.Println("floatArg value: ", *floatArg)
}
```

Il existe un autre moyen qui permet cette fois de récupérer directement les arguments dans des variables:

```go
package main

import (
	"flag"
	"log"
)

func main() {

	// crée les variables qui contiendront les arguments
	var boolArg bool
	var stringArg string
	var intArg int
	var uintArg uint
	var floatArg float64

	// attention: chaque paramètre est précédé d'un -
	flag.BoolVar(&boolArg, "b", false, "Argument de type booléen")
	flag.StringVar(&stringArg, "s", "", "Argument de type chaine de caractères")
	flag.IntVar(&intArg, "i", 0, "Argument de type entier signé")
	flag.UintVar(&uintArg, "u", 0, "Argument de type entier non signé")
	flag.Float64Var(&floatArg, "f", 0.0, "Argument de type nombre décimal")

	// parse les arguments saisis
	flag.Parse()

	//flag.PrintDefaults() // permet d'afficher le message d'aide, équivaut à ./programme --help ou ./programme -h

	log.Println("Nombre d'arguments: ", len(flag.Args()))
	log.Println("boolArg value: ", boolArg)
	log.Println("stringArg value: ", stringArg)
	log.Println("intArg value: ", intArg)
	log.Println("uintArg value: ", uintArg)
	log.Println("floatArg value: ", floatArg)
}
```