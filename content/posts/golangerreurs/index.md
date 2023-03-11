---
title: "Gérer les erreurs en Go"
date: 2023-02-19T15:58:07+01:00
draft: false
comment: false
---

En Go, les erreurs sont gérées de cette manière:

```golang

// squelette d'une fonction qui renvoi une erreur
func mafonction() error {
    if condition {
        return err // retourne l'erreur
    }
    return nil // retourne nil qui équivaux à aucune erreur
}

// code utilisé pour appeler la fonction:
err := mafonction()
if err != nil {
    // si la fonction retourne une erreur, la variable sera différente de nil
}
```

Cependant, cette manière de faire à ces limites, par exemple il n'est pas possible de créer des erreurs personnalisées avec cette technique. Pour créer des erreurs personnalisées, il faut créer ce qui est appelé des sentienelles. Il s'agit de variables qui seront des erreurs et qui contiendront un message d'erreur.

Voici un exemple basique d'une sentinelle:
```golang
package main
import (
	"errors"
	"log"
)
var (
	errConnexion = errors.New("Identifiant ou mot de passe invalide")
)
func accountConnection(username string, password string) error {
	var validUsername string = "name"
	var vaildPassword string = "pwd"
	if (username != validUsername) || (password != vaildPassword) {
		return errConnexion
	}
	return nil
}
func main() {
	err := accountConnection("name1", "pwd")
	if err != nil {
		log.Fatal(err)
	}
}
```
Dans l'exemple précédent, on crée simplement une nouvelle erreur, la variable est déclarée comme étant une variable globale. Une bonne pratique est de nommer les variables errors de la manière suivante: ErrNomErreur, dans l'exemple je l'ai appelée errConnexion ce qui permet de rendre la variable seulement accessible au niveau du package.

## Tester une erreur retournée

Pour comparer une erreur retournée, on peux tester simplement avec le code suivant:
```golang
if err == errConnexion {
    // il s'agit d'une erreur de connexion, on la traite
}
```

Ce type de code peux provoquer des problèmes, en reprendre l'exemple de la sentinelle, voici un exemple qui intègre une bonne pratique:
```golang
package main

import (
	"errors"
	"log"
)

var (
	errConnexion = errors.New("Identifiant ou mot de passe invalide")
)

func accountConnection(username string, password string) error {
	var validUsername string = "name"
	var vaildPassword string = "pwd"
	if (username != validUsername) || (password != vaildPassword) {
		return errConnexion
	}
	return nil
}
func main() {
	err := accountConnection("name1", "pwd")
	if err != nil {
		if errors.Is(err, errConnexion) { // ici, on test si l'erreur est bien une erreur errConnexion
			log.Fatal(err)
		}
		log.Fatal("Erreur inconnue")
	}
}
```
On remarque l'utilisation de errors.Is() au lieu de ==, l'opérateur == renvoi false si on le compare à l'erreur.

## Ajouter du texte en plus du message d'erreur

En Go, une erreur **doit** être traitée une seule fois. Il est possible d'ajouter des informations sur une erreur en utilisation la fonction Errorf() du package fmt.

Voici un exemple:
```golang

package main

import (
	"fmt"
	"log"
	"os"
)

var (
	fileOpenError string = "Impossible d'ouvrir le fichier"
)

func loadFile(fileName string) error {
	_, err := os.Open(fileName)
	if err != nil {
		
		return fmt.Errorf("%s %w", fileOpenError, err) // "wrap" les messages d'erreur: on ajoute un message au message initial
	}
	return nil
}
func main() {
	err := loadFile("fichierQuiExistePas.txt")
	if err != nil {

		log.Fatal(err)
	}
}
```


## Créer une structure pour les erreurs

Il est possible de réimplémenter le type error pour permettre ajouter des informations à une erreur, par exemple il est possible d'ajouter un code d'erreur à une erreur.

Voici un exemple:
```golang
package main

import (
	"fmt"
	"log"
)

type connexionError struct {
	message       string
	originalError error
    // ici il est possible d'ajouter d'autres champs comme un code d'erreur
}

func (e connexionError) Error() string {
	return fmt.Sprintf("%s", e.message) // retourne notre message d'erreur
}

func (e connexionError) Unwrap() error {
	return e.originalError // si il y a une erreur qui proviens d'un package de Go, on peux l'ajouter à notre structure personnalisée
}

func accountConnection(username string, password string) *connexionError {
	var validUsername string = "name"
	var vaildPassword string = "pwd"
	if username != validUsername {
		return &connexionError{message: "Nom d'utilisateur invalide", originalError: nil}
	}
	if password != vaildPassword {
		return &connexionError{message: "Mot de passe invalide", originalError: nil}
	}

	return nil
}

func main() {
	err := accountConnection("name", "pawd")
	if err != nil {
		log.Fatal("Erreur de connexion: ", err.message)
	}

	log.Println("Connexion réussie")
}
```

## Combiner les sentinelles et les structures d'erreurs

Je ne montre que l'exemple, il parle de lui même.

```golang
package main

import (
	"fmt"
	"log"
)

var (
	errBadLogin    connexionError = connexionError{message: "Nom d'utilisateur invalide", originalError: nil}
	errBadPassword connexionError = connexionError{message: "Mot de passe invalide", originalError: nil}
)

type connexionError struct {
	message       string
	originalError error
	// ici il est possible d'ajouter d'autres champs comme un code d'erreur
}

func (e connexionError) Error() string {
	return fmt.Sprintf("%s", e.message) // retourne notre message d'erreur
}

func accountConnection(username string, password string) *connexionError {
	var validUsername string = "name"
	var vaildPassword string = "pwd"
	if username != validUsername {
		return &errBadLogin
	}
	if password != vaildPassword {
		return &errBadPassword
	}
	return nil
}

func main() {
	err := accountConnection("name", "pawd")
	if err != nil {
		log.Fatal("Erreur de connexion: ", err.message)
	}
	log.Println("Connexion réussie")
}
```

## Sources

https://go.dev/blog/error-handling-and-go  
https://go.dev/blog/go1.13-errors  
https://levelup.gitconnected.com/go-error-best-practice-f0864c5c2385  
https://www.joeshaw.org/error-handling-in-go-http-applications/  
http://decouvric.cluster013.ovh.net/golang/gestion-des-erreurs-en-go.html  
https://peter.bourgon.org/blog/2019/09/11/programming-with-errors.html  
https://www.reddit.com/r/golang/comments/fg6527/simple_error_enum/  
https://dave.cheney.net/2016/04/27/dont-just-check-errors-handle-them-gracefully  
