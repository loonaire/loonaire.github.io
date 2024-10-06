---
title: "Powerflasher SOS Logger"
date: 2024-10-06T20:33:49+02:00
draft: false
comment: false
---

## Powerflasher SOS c'est quoi?

Il s'agit d'une ancienne librairie de gestion de logs avec les programmes actionscript. Le système permet de générer des logs à travers un xmlsocket ce qui veux dire que chaque paquet est du code xml.  
- L'archive du site officiel est disponible [ici](https://web.archive.org/web/20070221123717/http://sos.powerflasher.de/english/english.html): https://web.archive.org/web/20070221123717/http://sos.powerflasher.de/english/english.html.  
- La dernière version du programme fournie par Powerflasher est disponile [sur ce lien](https://web.archive.org/web/20150626170422/http://sos.powerflasher.com/fileadmin/sosmax/sos_max.zip).


## Le fonctionnement

Il existe 2 types de paquets, chaque message commence par la chaine ``!SOS`` et contient ensuite du code xml.  

Par défaut, l'outil utilise le port 4444.

### Les paquets de configuration

Les paquets envoyés à l'outil peuvent être des paquets de configuration, ils comportent les balises ``<setkey></setkey>``, ces balises servent à modifier la configuration de l'outil (par exemple fournir une couleur ou un type de message)

Exemple de message pour changer la couleur:
```xml
!SOS<setKey><name>Nom de la clé</name><color> Couleur associée à la clé</color></setKey>
```

### Les paquets de message
Les paquets de messages sont envoyés 

message d'exemple:
```xml
!SOS<showMessage key="nom de la clé"><![CDATA[ Message à afficher ]]></showMessage>
```

## La création d'un serveur pour récupérer les logs

La création d'un outil pour lire ces logs est plutot simple, pour commencer il faut un serveur TCP, nous n'avons pas besoin d'envoyer des informations au client, un simple serveur qui écoute de manière passive suffit.  

Ensuite, il faut parser les packets qui arrivent, si le paquet commence par ``!SOS``, il s'agit d'un paquet de log SOS, la suite du paquet sera donc du xml que l'on pourra parser simplement. Pour la partie CDATA, seule la partie entre crochets nous intéresse, en Golang le parser xml détecte automatiquement le CDATA.

