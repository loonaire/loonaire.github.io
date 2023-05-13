---
title: "Importer et exporter les données de firefox"
date: 2023-05-13T14:08:22+02:00
draft: false
comment: false
---

Voici plusieurs techniques pour récupérer les informations d'un profil de firefox.
Pour information, les profils sont stocké par défaut dans: ``%appdata%\Mozilla\Firefox\Profiles``, si le profil n'est pas présent dans ce dossier, il est possible de voir où est stocké le profil depuis firefox en tapant dans la barre d'url: ``about:support``.

> PS: je montre ici des techniques alternatives à la technique "officielle" de Mozilla pour le transfert de profil car je n'ai jamais réussi à les faires fonctionner correctement, cette technique est disponible [ici](https://support.mozilla.org/fr/kb/sauvegarder-restaurer-informations-profils-firefox).

> PS2: La méthode la plus simple est de passer par un compte Mozilla, tous se fait en restant en local sur votre réseau, il suffit de se connecter sur le premier appareil qui est celui avec les données à récuperer puis se connecter sur le second appareil qui recevra ces donnes.

> Les techniques présentées fonctionnes avec Firefox 113

## La manière simple

### Les marques pages

Pour les marques page, ils sont simplement exportable en allant dans ``Marque-pages -> Organiser les marque-âges`` puis dans la barre au dessus de la fenêtre qui s'ouvre, choisir ``Importation et sauvegarde``.  

- Pour exporter (sauvegarder) les données, choisir ``Sauvegarder...``
- Pour importer (restaurer) les données, choisir ``Restaurer`` puis ``Choisir un fichier...``

### Les mots de passe

> Attention: cette technique est déconseillé, si vous exportez les mots de passe dans un fichier csv, pensez à **SUPPRIMER** le fichier lorsque vous n'en n'avez plus besoin. Mozilla déconseille cette méthode et bloque un maximum cette fonctionnalité.

Pour les mots de passe, il faut aller dans l'outil ``Mots de passe``.

- Pour sauvegarder les mots de passe, il faut aller sur le bouton ``...`` en haut à droite puis choisir ``Exporter les identifiants...``.
    > Attention: Pour pouvoir exporter les identifiants il faut s'identifier avec l'utilisateur connecter, il n'est pas possible de le faire avec une autre session qui aurais les droits administrateur.

- Pour importer les identiifants, cela se fait en 2 étapes:
    - Tout d'abord, il faut aller dans ``about:config`` puis chercher l'option ``signon.management.page.fileImport.enabled``, cette option doit être mise à ``true``
    - En revenant dans l'outils Mots de passe, sur le bouton ``...`` l'option ``Importer depuis un fichier...`` est désormais disponible, il suffit de choisir le fichier et l'import des identifiants se fera tout seul.


## En Récupérant les informations depuis les fichiers d'un profil

Cette technique est utile si vous ne pouvez pas démarrer firefox par exemple, elle permet également de transférer les informations sans avoir besoin d'interaction de la part de l'utilisateur.

### Les marque-pages

Pour les marque-pages, il faut copier les fichiers ``places.sqlite`` et ``favicons.sqlite`` de l'ancien profil vers le nouveau.

### Les mots de passe

Pour les mots de passe, il faut copier les fichiers ``key4.db`` et ``logins.json`` de l'ancien profil vers le nouveau.

### Les autres fichiers

Pour d'autres fichiers je redirige vers la documentation de Mozilla concernant les fichiers des profils disponible [ici](https://support.mozilla.org/en-US/kb/recovering-important-data-from-an-old-profile).