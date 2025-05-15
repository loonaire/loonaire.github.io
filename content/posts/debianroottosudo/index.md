---
title: "Debian: Remplacer le compte root par sudo"
date: 2025-05-15T19:14:29+02:00
draft: false
comment: false
---

J'ai appris il y a peu que pour remplacer le compte root par sudo sur Debian il ne faut pas saisir de mot passe pour root au moment de l'installation. Je souhaite corriger ce problème sur certains serveur, la démarche est plutot compliquée à trouvée alors la voici:

- Commencer par installer sudo sur la machine: ```apt install sudo```
- Ajouter l'utilisateur qui utilisera sudo pour être root: ```usermod –aG sudo loonaire```
- A partir de cette étape, l'utilisateur peux utiliser sudo pour avoir accès au mode super utilisateur.
- Pour désactiver le compte root, il faut utiliser la commande: ```usermod -p '!' root```

Pour réactiver le compte root plus tard, il faut utiliser la commande: ```sudo passwd root```

Cet article est entièrement pompé sur ce lien: [Guide sur it connect](https://www.it-connect.fr/linux-comment-desactiver-le-compte-root-sur-debian-12/)