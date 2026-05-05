---
title: "Docker: Partie 1 l'installation sur linux"
date: 2026-04-24T20:39:07+02:00
draft: false
comment: false
---

## Pourquoi cette série

Ca fait longtemps que je souhaite faire une série d'article sur docker, avec mes besoins croissant pour cette techno, j'ai décidé d'en faire une série et comment l'utiliser dans un environnement en prenant en compte l'aspect sécurité.  

> Attention: Ici je ne vais parler que de Docker et non des modules d'adaption de podman aux conteneurs Docker par exemple.

## Les différentes versions de Docker sous linux

Sur Debian et Ubuntu, Docker est disponible en 2 versions différentes sur certaines distributions:
- Le paquet docker.io qui est nativement disponible sur la distribution, ce paquet est rendu disponible via le dépot de paquet par défaut de la distribution et suivant le cycle de mises à jours, la version peux varier et ne pas être à jour. De plus, cette version à des dépendances avec les librairies fournies par les dépots de base de la distribution. Ces dépendances peuvent entrainer des conflits entre docker et la version de la lib fournie par la distribution (ce problème est un des princpaux problèmes de linux pour moi, essayez par exemple de lancer un ancien flash player linux sur linux aujourd'hui...). De plus la distribution ne suit pas les versions LTS de Docker ce qui peux poser des problèmes
- L'ajout du dépot qui package ses propres dépendances

Le plus simple est d'utiliser la version du dépot fourni par Docker. Mais j'ai tout de même un doute, la version fournie dans les dépots doit tout de même être aussi bien, pourquoi utiliser celle des dépots Docker? est-ce qu'il y a vraiment besoin de toujours avoir la dernière version et dupliquer les dépendances sur le système hote en production?
Le problème est à la fois lié à Golang et à Debian. Ubuntu à moins de problème de décalage de version.  
Se pose également l'interet d'utiliser Docker sur un système de base RedHat (hors Fedora), même si podman est à privilégier sur ces système, Docker est installable mais est-ce vraiment une bonne idée? Je ne pense pas.  
Au final, la meilleur distribution pour installer Docker dessus est... Alpine linux, même si ici je serais sur une base ubuntu. Pourquoi?
- Le système est en semi "rolling release", les nouvelles releases sont fréquentes, les upgrades très simple et à ma connaissance il n'y a quasiment aucune casse suite aux mises à jour.
- Le système est la base de beaucoup de conteneurs car léger donc en hote avoir quelque chose de très légé est interesssant
- Docker est disponible nativement dans les dépots, il n'y a pas de dépot supplémentaire à installer  
En revanche l'utilisation est un peu plus complexe et il faut configurer manuellement Docker, peux être dans un futur article.  

A noter également: Docker nécessite iptable et iptable doit être correctement configurer pour éviter les problèmes de sécurité avec Docker.

## Installation 

Pour l'instant, l'objectif est de faire une simple installation. Le guide est disponible sur [https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/) et suivre les étapes.  

## Le post-install

Après l'installation, il faut démarrer le service avec la commande:
```sh
sudo systemctl start docker
```

Ensuite la commande suivante lance un container et permet de tester l'installation:
```sh
sudo docker run hello-world
```

Si on ne souhaite pas que docker démarre automatiquement, lancer la commande:
```sh
sudo systemctl disable docker
sudo systemctl disable containerd
```

Par défaut, une interface est crée sur le réseau 172.17.x.x, on reviendra sur ce sujet plus tard avec la gestion des interfaces réseaux.

## Le post install

La documentation officielle est diponible [ici](https://docs.docker.com/engine/install/linux-postinstall).

### Le passage en rootless

Cette étape est discutable mais très importante pour éviter les problèmes d'évasions de conteneurs. Le passage en rootless permet de lancer docker sur l'hote sans avoir besoin des droits root (avec sudo ou connexion au compte root via su). Dans les inconvénients, il ne sera plus possible de rediriger les ports inférieurs à 1024 sur l'hote, il faudra passer par un reverse proxy ou une autre ruse comme des règles de pare feu, nous verrons ca plus tard. L'accès au matériel peux également devenir plus compliqué à cause du fonctionnement de certains driver, faire tourner du matériel spécifique dans un conteneur (surtout docker) peu être très compliqué et devient impossible avec le mode rootless (un conteneur ne doit pas remplacer un système de gestion de paquet, si il faut faire tourner du matériel spécifique il faut plutot être en installation native ou éventuellement en vm avec un bypass).

> les instructions sont disponibles [ici](https://docs.docker.com/engine/security/rootless/)

On va commencer par arrêter docker et coupeur le socket:
```sh
sudo systemctl disable --now docker.service docker.socket
sudo rm /var/run/docker.sock
```

Il faut installer le packet uidmap pour avoir accès aux outils nécessaires pour la gestion des permissions gid et uid:
```sh
sudo apt install uidmap
```
Sur Debian et les distributions redhat et leurs dérivées, un script est diponible, il suffit de le lancer:
```sh
/usr/bin/dockerd-rootless-setuptool.sh install
```

Après le lancement du script, avec la commande ```docker info``, on obtient les lignes suivantes (le context doit être en rootless):
```sh
Client: Docker Engine - Community
 Version:    29.4.2
 Context:    rootless
 Debug Mode: false
```

> Désormais docker sera executé en tant que service pour l'utilisateur qui a executé le script.

L'état du service peux être vérifier avec la commande:
```sh
systemctl --user status docker
```
> Attention: ``systemctl status docker`` renvois vers la version de base de l'installation qui se lance en tant que root, ce service doit être évité le plus possible.


## Le fichier daemon.json

Le fichier daemon.json correspond au fichier de configuration de docker, il permet de configurer des paramètres par défaut pour les conteneurs.  
En fonction de l'installation, il y a 2 emplacements pour ce fichier qui sera à créer:
- En installation de base non rootless : ``/etc/docker/daemon.json``
- En rootless : ``~/.config/docker/daemon.json``

Le conseil de base est de configurer au minimum les logs en json-file;
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

[Lien pour la documentation de gestion des logs de docker](https://docs.docker.com/engine/logging/configure/#configure-the-default-logging-driver)

> Pour rappel, les logs doivent normalement être gardés pendant minimum 6 mois d'après les guides de l'ANSSI


### Changer la plage par défaut du bridge

En cas de problème, par exemple si le réseau utilise la plage 172.17.0.0/16, les conteneurs Docker ne redirigeront pas correctement vers les ip du réseau.
Par exemple si un ordinateur avec l'ip 172.17.255.250 se connecte à un conteneur qui contient un service web qui a pour ip 172.17.0.2 et que le conteneur est en mode bridge, la machine hote du conteneur peux avoir une autre ip et être dans un autre VLAN sans problème. Cependant lorsque l'ordinateur se connectera, il initiera la connexion vers le service web mais le service web ne lui renverra aucun paquet (pas d'initialisation de la connexion TCP), l'accès au service web sera inaccessible car le conteneur pensera que l'ip de l'ordinateur sur trouve sur son bridge et est donc un autre conteneur.

Pour changer la valeur, dans le fichier ``daemon.json`` il faut ajouter la ligne suivante en adaptant les valeurs et le masque (il faut saisir la première ip du réseau), ici un exemple pour passer les ip dans la plage d'adresse privée 192.168.0.X/24:
```json
{
"bip":"192.168.0.1/24"
}
```
> Si le fichier n'est pas vide, il faut ajouter la ligne dans les accolades du fichier déja existant.


## Conclusion

Voici un rapide résumé de l'installation de Docker, idéalement à reproduire sur un environnement de production. Il y aura d'autres articles sur l'utilisation.