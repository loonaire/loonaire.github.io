---
title: "L'Oauth et les outils de gestion de ticket (redmine/glpi)"
date: 2024-01-22T19:07:27+01:00
draft: true
comment: false
---

Un petit article sur un point de vue sur l'utilisation actuelle de l'Outh ainsi que sur les outils hébergés en local qui l'utilisent.

## Le contexte et l'origine de l'article

### La problématique

Actuellement, je suis en charge de maintenir une VM qui utilise le logiciel Redmine. L'outil est plutot bon, même si à l'origine il sert plutot pour la gestion de projet, il est très simple à détourner pour le transformer en outil de gestion de ticket et récupération de ticket via mail. Cependant, suite au blocage de Microsoft de l'authentification basique sur les services Azure, il faut passer en Oauth, c'est pour cela qu'il y a tout juste un an, le plugin Redmine Helpdesk a été installé, bien que payant, le plugin fonctionnait très bien et permettais de récupérer les mails via des connexion aux adresses mails en OAuth. Nous voici donc dans les 3 premières semaines de janvier 2024, avec un plugin qui ne fonctionne plus du tout et à la recherche d'un nouveau plugin.

### La résolution

Pour résoudre il y avais 2 choix:  
- Corriger le problème du plugin redmine helpdesk, comme le plugin est payant, il a un support qui je suppose est effectué par une seule personne en Russie, la société a été délocalisée en Arménie pour éviter les sanctions mais la personne est Russe, en plus le plugin log mal les informations et la manière de fonctionner du plugin est très floue, ce plugin devais être changé quoi qu'il arrive.
- Le choix sélectionné a été de changé le plugin en passant à redmine_oauth, ce plugin est open source, certe nous sommes dépendant du mainteneur sur github mais on peux contribuer et nous ne dépandons pas d'un support médiocre. Ce plugin apporte en bonus le SSO et... ne rajoute pas une surcouche à redmine comme redmine helpdesk mais s'interface avec la manière de récupération des mails intégrée à redmine ce qui fait qu'il suffit d'utiliser la documentation de redmine pour comprendre comment le paramétrer, c'est un gain de temps et de simplicité. Cette solution a un coup: le besoin de maintenir une version de redmine à jour pour permettre les mises à jour du plugin.

## La connexion OAuth

Le problème est la connexion OAuth, dans le cas de GLPI, il faut utiliser le plugin OAuth Imap avec une incription d'applicaiton dans Azure, dans le cas de Redmine, nous avons plusieurs choix, le premier avec redmine helpdesk qui reprend GLPI, une inscription d'application et passage via https. Le second choix est redmine_oauth qui demande inscription d'application ainsi qu'une autorisation spéciale dans Azure, cette méthode utilise le mode IMAP et ne demande aucun mot de passe ce qui est super intéressant.

## Les conséquences

### Les services hébergés

La connexion OAuth implique une mise à jour régulière, surtout avec Microsoft qui change régulièrement les protocoles. Ceci implique beaucoup de mises à jours:
- Mise à jour des plugins régulières et préventives
- Mise à jour de l'application régulière, les plugins suivent les dernières versions disponibles et ne sont souvent pas compatible avec les anciennes versions (c'est le cas de beaucoup de plugins sur GLPI)
- La mise à jour de l'environnement sur lequel tourne l'application, il faut que le système hôte puisse recevoir des mises à jours pour les runtimes et les dépendances qui permettent le fonctionnement applications.

### Le choix de l'hôte

Ces éléments ont des conséquences sur le choix de l'hôte, à l'origine choisir une distribution très stable type Reh Hat et ses dérivées (RHEL,CentOS,AlmaLinux,RockyLinux,...) était un bon choix: seulement des mises à jour de sécurité, pas besoin de mises à jour de l'application, ce fonctionnement est intéressant. Cependant l'OAuth et les fournisseurs cloud changent la donne, ils évoluent rapidement, ainsi une distribution Red Hat like devient inutile: on ne peux pas faire d'upgrade de distribution officiellement, il faut réinstaller la nouvelle version. Ce n'est pas le cas de Debian: tous les 2 ans la distribution passe à la version suivante et met à jour en même temps les paquets. L'avantage? 2 ans c'est également en moyenne le temps de durée de la version de compatibilité de l'application hébergée. Ainsi, toutes les applications qui reçoivent régulièrement des mises à jour doivent plutot passer de distribution RHEL like vers Debian.



## Conclusion

Pour conclure, le passage à l'OAuth change beaucoup de chose sur la manière de mise en production des applications en entreprise, il faut désormais prendre en compte et anticiper ces mises à jour ce qui implique du travail en plus.
