---
title: "Ecoute réseau avec Pktmon"
date: 2024-04-01T21:45:27+02:00
draft: false
comment: false
---



Il y a longtemps que je souhaitais créer une page sur la capture de paquet sous windows de manière native sans wireshark.

> Toutes les commandes doivent être lancées dans une invite en mode administrateur
 
> Ces outils ne permettent pas de sniffer le traffic local de l'ordinateur (localhost / 127.0.0.1)

## netsh

netsh peux servir à écouter le traffic.

Démarrer la capture:
```sh
netsh trace start persistent=yes capture=yes tracefile=c:\trace\firsttrace.etl
```

Arreter la capture:
```sh
netsh trace stop
```

Pour ensuite voir les trames avec wireshark (avec la version portable ou sans avoir besoin d'installer les outils pour écouter toutes les cartes réseaux), il faut convertir la trame etl en pcap avec l'outil etl2pcap de microsoft [disponible ici](https://github.com/microsoft/etl2pcapng).

### Liens utiles sur netsh

- Microsoft Network Monitor pour lire les fichiers etl: [https://www.microsoft.com/en-in/download/details.aspx?id=4865](https://www.microsoft.com/en-in/download/details.aspx?id=4865)

- [https://community.progress.com/s/article/How-to-run-a-NETSH-Trace](https://community.progress.com/s/article/How-to-run-a-NETSH-Trace)

- [https://github.com/microsoft/etl2pcapng](https://github.com/microsoft/etl2pcapng)

## PktMon

L'outil pktmon est présent dans Windows 10 depuis la version 1809, cet outil remplace le netsh trace et apporte l'ajout de filtre et simplifier la gestion des captures.
Par défaut, pktmon ne capture pas les données des paquets, il ne capture que les entêtes.

- L'équivalent de la commande netsh trace:
```sh
pktmon start -c
```

- Sauvegarder vers un fichier spécifique (par défaut c:/Windows/System32):
```sh
pktmon start -c --file-name <nom du fichier>.etl
```


- Arreter la capture:
```sh
pktmon stop
```
- Afficher la liste des cartes réseaux:
```sh
pktmon list -a
```

> Avec l'argument --json il est possible de récupérer un format exploitable en json, idéal pour les scripts ou autres programmes

- Capturer sur une interface spécifique:
```sh
pktmon start --comp <id interface>
```
> Plusieurs interfaces peuvent être précisées en les séparant avec des espaces



### Les filtres

Il est possible d'ajouter des filtres à la capture pour diminuer le nombre d'éléments enregistrés.

- Lister les filtres actifs:
```sh
pktmon filter list
```

- Retirer tous les filtres:
```sh
pktmon filter remove
```

- Ajouter un/des filtre(s):
```sh
pktmon filter add [nom du filtre] <arg filtre>
```

> Le nom du filtre est optionnel

> Les filtres sont cumultables, un filter peux filtrer un réseau associé à un/des port(s) ainsi qu'à un protocole.

- Filtre par ip ou réseau:
```sh
pktmon filter add -i <ip>[/subnet]
```
- Filtre par port:
```sh
pktmon filter add -p <num port>
```

- Filtre pour un protocole de transport:
```sh
pktmon filter add -t TCP
```

- Filtrer les paquets d'un VLAN:
```sh
pktmon filter add -v 
```


L'aide pour les filtres peux être consulté avec la commande:
```sh
pktmoon filter add help
```


### Commandes à connaitre

- Convertir un fichier etl en pcap:
```sh
pktmon etl2pcap <fichier>.etl [-o <nom du fichier en sortie>.pcap]
```

- 