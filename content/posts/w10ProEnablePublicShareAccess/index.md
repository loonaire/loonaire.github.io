---
title: "Activer l'accès aux partages smb sans mot de passe sur Windows 10 Pro / Education"
date: 2022-09-16T18:01:15+02:00
draft: false
comment: true
---

En voulant mettre en place un partage accessible sans mot de passe, je me suis retrouvé face à un problème d'accès avec Windows 10 Education. Le partage était situé sur un Windows 10 Famille, un autre ordinateur pouvais accéder à ce partage mais impossible depuis un Windows 10 Education.
Le problème viens de la désactivation de l'accès aux partages sans mot de passe, ce problème est résolu en modifiant les GPO de l'ordinateur.

Résolution:
- Ouvrir gpedit.msc 

- Aller dans Configuration **Ordinateur -> Modèles d'administration -> Réseau -> Station de travail LANMAN**

- Mettre la stratégie **Activer les ouvertures de session invité non sécurisées** sur Activer

Voila, si tout est bien configuré, l'accès aux fichier partagés sans mot de passe devrais être accessible.

