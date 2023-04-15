---
title: "Analyse d'un algorithme de chiffrement de mot de passe"
date: 2023-04-15T10:51:13+02:00
draft: true
comment: false
---

## Disclaimer

> Je vais analyser une fonction qui a réellement été utilisée en production pour chiffrer les mots de passes. Cette fonction comporte plusieurs faillses et problèmes qui font quelle ne serait pas réutilisable aujourd'hui.

 ## Introduction

 Récemment en cherchant des informations sur le jeu Dofus entre 2003-2006, j'ai décidé de regarder un peu le code du jeu et j'ai également eu besoin de comprendre le fonctionnement du code, cela ma permis d'apprendre un peu le fonctionnement d'ActionScript et plus particulièrement de flash player. Je ne vais pas détailler ici le fonctionnement client/serveur complet, je ne m'intéresse qu'à l'algorithme de mots de passe.  
 
 La fonction qui va être étudiée est celle qui permet au client de chiffrer le mot de passe avant de l'envoyer au serveur, je suis sur quelle à été utilisée de la beta v5b du jeu (decembre 2003/janvier 2004) et quelle à été utilisée au moins jusqu'à la version 1.9.1 (septembre 2005), je pense que la fonction à changé fin 2005 avec la version 1.10 ou 1.11 qui ont apportées des modifications sur les comptes de jeu, la version 1.13 (avril 2006) possédant une autre fonction de chiffrement qui est identique à celle utilisée au moins jusqu'à la version 1.29 (sortie en 2010).

 ## La fonction

 Voici la fonction utilisée, elle est traduite en langage Go avec possiblement quelques optimisations.
```go
 func CryptPassword(password string, key string) string {
	var HASH string = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-_"
	password += "\n"
	var saltPassword string = ""
	for i := 0; i < len(key); i++ {
		saltPassword += string(HASH[(password[i%len(password)]^key[i%32])%64])
	}
	var cryptedPassword string = ""
	for i := 0; i < len(saltPassword); i++ {
		cryptedPassword += string(HASH[(saltPassword[len(saltPassword)-i-1]^key[(i+8)%32])%64])
	}
	return cryptedPassword
}
```

### **Démonstration de la fonction:**

Voici le résultat obtenu en utilisant la fonction ci dessus:

``` 
Clé pour le chiffrement du mot de passe: lhyflzbhfvlsmjicxhimofqwxdkgssha  
Mot de passe saisi: test  
Mot de passe chiffré: d1zByfy6ClnVHIZZaUUIekqsUawRFrmr
```

### **Analyse**
En regardant la fonction, on peux la séparer en 3 parties:
- La première partie déclare un tableau HASH qui contient les caractères pour une base 64 customisée et elle ajoute à la fin du mot de passe un caractère de retour à la ligne, ce qui est que le mot de passe sera toujours de la forme: motdepasse + \n.
- La seconde partie est la première boucle for, cette boucle va ajouter des caractères au mot de passe pour avoir la même taille entre le mot de passe et la clé utilisée. On remarque également qu'une opération XOR est utilisée.
- La troisième partie concerne la troisième boucle for, elle prend le mot de passse obtenu à la deuxième partie et réeffctue un XOR pour chiffrer la chaine.   

Les failles de cette techinque:
- La longueur de la clé est toujours modulo 32 ce qui veux dire que la clé est toujours de 32 caractères. De plus, il semble que la clé ne contienne que les caractères a-z.
- La longueur maximale du mot de passe est de 31 caractères (surement 24 à l'époque) car il faut ajouter le caractère \n à la fin du mot de passe.
- Le caractère \n sera toujours présent et chiffré de la même manière au moment de la première boucle.  

Au final, la fonction ne fait qu'utiliser un XOR cypher pour chiffrer le mot de passe, théoriquement avec une clé toujours aléatoire et sans l'ajout du caractère \n il serait impossible de "craquer" l'algorithme car la première boucle qui sale le mot de passe est une technique avec destruction des données... à condition de ne pas avoir de caractère fixe!  

### **Le problème du stockage de mot de passe**

Cette fonction montre également une faiblesse: comme elle dépend de la clé utilisée pour le chiffrement, il faut à un moment avoir les mots de passe en clair coté serveur pour vérifier si le mot de passe est bon. Bien entendu, je parle ici de la période 2000-2005 qui était une période où la qualité du  chiffrement n'était pas une priorité, cependant comme le résultat dépend de la clé et si les clés étaient aléatoire, il y a plusieurs possibiltiés:
- Mots de passe stockés en clair dans la base de données et/ou avec un chiffrement réversible et ensuite recalculé avec la clé et le mot de passe en clair coté serveur.
- Système de déchiffrement du mot de passe à partir de la clé permettrais de comparer le mot de passe obtenu (en appliquant l'agorithme de chiffrement) à un mot de chiffré, cette option me semble possible, est mon objectif actuel et expliquerai les "failles" de l'algorithme.


## Le déchiffrement

Pour le déchiffrement, on opère de manière inverse au chiffrement. On commence donc par la seconde boucle puis on fait la première boucle.  

Comme les 2 boucles sont similaires, les techniques sont les mêmes:
- On "inverse" l'opération XOR, pour rappel, les opérations XOR peuvent êtres inversées pour retrouver les éléments de départ: **res = mdp[i] ^ clé[i] -> mdp[i] = res ^ clé[i]**.
- Pour les éléments du tableau, c'est plutot simple, comme la fonction de chiffrement renvoi le caractère à la position i, pour déchiffrer on prend le code ascii du caractère et on le xor avec la valeur asscii de la clé à la position i. Au final on obtiendra un entier qui correspond à une lettre en ascii.

Ensuite, la chaine est inversée donc il faut prévoir d'inverser la chaine.

Pour retirer le sel du mot de passe, il "suffit" de faire quasiment la même chose que le xor du déchiffrement.

### Le code de déchiffrement

Voici le code qui permet de déchiffrer un mode de passe, attention, il ne marche pas totalement, je doit passer à coté de quelque chose, les nombres ne sont pas bien restitués:

```go
func decipherPassword(passwordCrypted string, key string) string {
	var HASH string = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-_"
	var passwordDecipherReverse string = ""
	// décrypte le mot de passe
	for i := 0; i < len(passwordCrypted); i++ {
		decryptedValue := (strings.Index(HASH, string(passwordCrypted[i])) % 64) ^ int(key[(i+8)%32])
		passwordDecipherReverse += string(byte(decryptedValue))
	}
	// on obtiens la chaine inversée, on doit donc l'inversée
	var passwordDecrypted string = ""
	for i := len(passwordDecipherReverse) - 1; i >= 0; i-- {
		passwordDecrypted += string(passwordDecipherReverse[i%32])
	}
	return passwordDecrypted
}
```

Ensuite, on doit retirer le sel du mot de passe:
```go
func unsaltPassword(passwordDecrypted string, key string) string {
	var HASH string = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-_"
	var unsaltPassword string = ""
	for i := 0; i < len(key); i++ {
		unsaltChar := (strings.Index(HASH, string(passwordDecrypted[i%32])) % 64) ^ int(key[i%32])
		unsaltPassword += string(byte(unsaltChar))
	}
	return unsaltPassword
}
```

### Le résultat

Voici le résultat avec le mot de passe bonjour:

```
Clé pour le chiffrement du mot de passe:  gnhmmotpfilgyllplqwujeuioygklyua
Mot de passe saisi:  bonjour
Mot de passe chiffré:  0bbdBgBEMzgtSFWgz8DCcAserjPofiww
Mot de passe déchiffré:  fbghcAgvegcnwzEvoEzFfqhJnwjbdmhR
Mot de passe avec retrait du sel:  bonjourebonjourebonjourJbonjourJ
```

Bien entendu, le code de déchiffrement ne fonctionne pas à 100% mais le déchiffrement fonctionne bien sur les mot de passe sans chiffres. On remarque que le mot de passe est séparé par un caractère: J (même si il est séparé par des e, le vrai caractère semble être le J).

Ceci crée donc une faille dans le chiffrement, il suffit de splitter la variable avec le caractère J et la valeur nous est donnée.

## Conclusion

La formule de chiffrement est bonne, surtout pour un client Flash Player qui n'intégrait pas de fonction crypto nativement cependant l'utilisation du système de xor à quelques problèmes et impose des contraintes spécifiques (difficultées à inverser la chaine et donc obligation de garder les mots de passe en clair quelque part). Je ne sais pas si l'erreur sur le déchiffrement viens de moi ou est normal, si il est normal tant mieux, sinon il faudra que je me penche sur cette erreur.