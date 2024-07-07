---
title: "La gestion de certificats en entreprise avec l'ADCS et openssl"
date: 2024-07-07T12:51:54+02:00
draft: false
comment: false
---

Pour un besoin pro, j'ai eu à apprendre le fonctionnement des certificats en entreprise, j'ai décidé d'apprendre le fonctionnement des certificats en utilisans le role ADCS de Windows Server (surement le plus utilisé).

## Les 2 types de générations de certificats

On peux dissocier les certificats en 2 catégories:

### Les certificats auto signés

Ce type de certificat est plus simple à générer, sur Windows avec IIS la console IIS permet de générer des certificats auto signés, sous openssl nous pouvons utiliser la commande suivante:
```sh
openssl req -new -nodes -x509 -keyout /etc/ssl/private/domain.key -out /etc/ssl/certs-auto/domain.cert -newkey rsa:2048
```
> Ici les chemins peuvent être changé, il en va de même pour le type de chiffrement de la clé, rsa est déprécrié et 2048bits est aujourd'hui trop peu (mais l'augmenter augmente le temps de génération de la clé)

Voici le schéma suivi pour l'autosignature de fichier:
![alt text](images/diagramme1.png)


Le problème de ce fonctionne est que les clients externes à la machine qui n'ont pas le certificat d'installés verrons un messgae indiquant que le certificat n'est pas valide car il n'a pas été signé par une vraie autorité de certification, pour éviter ce problème, il faut utiliser l'autre méthode de génération de certificat.

### Les certificats générés par une autorité de certification

Dans cette méthode, le système de génération est globalement le même à la différence que le certificat sera validé par une autorité de certificat qui permettra la validation et la diffusion du certificat pour d'autres utilisateurs.
Voici dans les grandes lignes le fonctionnement de ce système (j'ai volontairement simplifié des étapes):
![alt text](images/diagramme2.png)

> Sur le schéma précedent, le certificat est généré sur la machine cible mais il peux être généré sur un autre ordinateur, la seule condition est de garder le fichier .key qui est la clé privée et qui pourra reservir en cas de besoin de regénérer de renouveler le certificat.

Je détails cette méthode en dessous

## Les formats de fichier de certificat

- key : Fichier qui contient la clé privée du certificat, commence par ``-----BEGIN ENCRYPTED PRIVATE KEY-----``
- csr : Contient une demande de création d'un certificat, commence par ``-----BEGIN CERTIFICATE REQUEST-----``
- pem,crt, cer, cert : Contient un certificat, ces fichiers commencent par ``---- BEGIN CERTIFICATE----``
- pfx : Contient un certificat qui utilise la norme PKCS#12, il contient une clé privée ainsi qu'une clé publique

## Générer une demande

> Pour des raisons pratique, je n'explique que la démarche avec openssl mais la création de clé est possible avec Windows

Avec openssl il faut utiliser la commande suivante pour générer une clé privée ET un fichier csr qui sera à envoyer à l'authorité de certification:
```sh
openssl req -sha256 -newkey rsa:2048 -keyout domain.key -out domain.csr
```
> Pour retirer l'obligation d'ajouter une passphrase, il faut ajouter l'argument ``-nodes``

Récupérer le fichier csr généré sur l'autorité de certificat (ou sur un autre poste qui possède une console MMC avec le composant Autorité de certification) puis aller sur le nom du serveur -> Toutes les tâches -> Soumettre une nouvelle demande

![alt text](images/adcs1.png)

> Dans mon cas, j'ai un message d'erreur m'indiquant que le certificat n'utilise pas de modèle disponible sur l'authorité de certification

![alt text](images/adcserror.png)

> **Eviter le message d'erreur**
>
>La solution est disponible [via ce lien](https://vxav.fr/2020-02-18-how-to-sign-a-certificate-with-no-template-information-on-a-microsoft-ca/). Il faut générer le certificat via l'invite de commande.
>
> - Ouvrir une invite de commande en mode administrateur
> - Pour afficher la liste des templates disponible et leur accès, utiliser la commande: ``certutil -CATemplates -Config <Nom de la machieb>\<Nom de l'autorité de certification>``
> - Pour générer la réponse du certificat, on va utiliser la commande certreq en indiquant le modèle de certificat que l'on va utiliser: ``certreq -attrib "CertificateTemplate:WebServer"``, ensuite il est demander de choisir le fichier, ensuite il sera demande un nom de fichier pour le fichier .crt


## Les commandes utiles sous openssl

Convertir un certificat et sa clé en pfx:
```sh
openssl pkcs12 -inkey domain.key -in domain.crt -export -out domain.pfx
```


## Liens

- [https://www.it-connect.fr/comment-convertir-un-certificat-au-format-pfx/](https://www.it-connect.fr/comment-convertir-un-certificat-au-format-pfx/)

- [https://vxav.fr/2020-02-18-how-to-sign-a-certificate-with-no-template-information-on-a-microsoft-ca/](https://vxav.fr/2020-02-18-how-to-sign-a-certificate-with-no-template-information-on-a-microsoft-ca/)

- Créer des clés sur Windows: [https://serverfault.com/questions/849766/creating-private-key-to-certificate](https://serverfault.com/questions/849766/creating-private-key-to-certificate)

- Générer un csr depuis Windows: [https://www.tbs-certificats.com/FAQ/fr/windows-csr-mmc.html](https://www.tbs-certificats.com/FAQ/fr/windows-csr-mmc.html)

- Demander un certificat au gestionnaire de certificat windows [https://learn.microsoft.com/fr-fr/system-center/scom/obtain-certificate-windows-server-and-operations-manager?view=sc-om-2022&tabs=Enterp%2CEnter](https://learn.microsoft.com/fr-fr/system-center/scom/obtain-certificate-windows-server-and-operations-manager?view=sc-om-2022&tabs=Enterp%2CEnter)

- Générer un pfx [https://www.tutos.eu/6345](https://www.tutos.eu/6345)

- Générer un certificat autosigné pour IIS [https://www.it-connect.fr/comment-creer-un-certificat-auto-signe-avec-iis/](https://www.it-connect.fr/comment-creer-un-certificat-auto-signe-avec-iis/)

- Documentation Microsoft pour générer un certificat autosigné [https://learn.microsoft.com/fr-fr/entra/identity-platform/howto-create-self-signed-certificate](https://learn.microsoft.com/fr-fr/entra/identity-platform/howto-create-self-signed-certificate)

- Gestion des certificats avec une authorité openssl [https://www.linuxtricks.fr/wiki/openssl-creation-de-certificats-et-ca-autosignes](https://www.linuxtricks.fr/wiki/openssl-creation-de-certificats-et-ca-autosignes)

- Exporter un certificat sur une autorité Windows [https://pbarth.fr/node/447](https://pbarth.fr/node/447)

- Générer un csr avec openssl [https://www.it-connect.fr/generer-une-demande-de-certificat-csr-avec-openssl/](https://www.it-connect.fr/generer-une-demande-de-certificat-csr-avec-openssl/)

- Mise en place de l'ADCS [https://www.it-connect.fr/adcs-creer-une-autorite-de-certification-racine-sous-windows-server/](https://www.it-connect.fr/adcs-creer-une-autorite-de-certification-racine-sous-windows-server/)

- [https://pbarth.fr/node/429](https://pbarth.fr/node/429)