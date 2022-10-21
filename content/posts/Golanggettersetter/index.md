---
title: "Go: Les fonction Get et Set des structs"
date: 2022-10-21T15:57:16+02:00
draft: false
comment: false
---

Voici un article sur la mise en place de Getter et setter en Go. Go à un fonctionnement spécifique concernant ces fonctions, pour les setters, les fonctions doivent être nommées SetAttribut, pour les getter, il faut nommer les fonctions Attribut sans utiliser le mot Get.

Un exemple avec la classe Point:
```golang

type Point struct {
    x int
    y int
}

// Getter de x
func (p Point) X() int{
    return p.x
}

// Setter de x
func (p *Point) SetX(newValue int){
    p.x = newValue
}

// Getter de y

// Getter de x
func (p Point) Y() int{
    return p.y
}

// Setter de x
func (p *Point) SetY(newValue int){
    p.y = newValue
}

```

Pour l'appel cela se fera de cette manière:

```golang

var p Point

p.SetX(5)

fmt.Println(p.X())

```