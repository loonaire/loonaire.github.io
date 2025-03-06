---
title: "Optimiser l'installation de Windows 10 (et 11?)"
date: 2025-03-06T10:59:55+01:00
draft: false
comment: false
---

Quelques éléments à faire pour l'optimisation de windows, l'objectif n'est pas d'avoir quelque chose de super optimiser mais d'avoir quelques optimisation pour la durée de vie des composants et la réactivité du système:

## Désactiver le mode veille (dans une console en mode admin):
```sh
powercfg /hibernate off 
```

Pour les pc portables de 2020-2023, diminuer la fréquence du processeur pour diminuer la température du cpu:
- Dans options d'alimentation -> Modifier les paramètres du mode d'alimention -> Modifier les paramètres d'alimentations avancés
- Passer la fréquence maximal à 3500MHz sur batterie et 4000MHz sur secteur, ces valeurs peuvent être plus faible si l'ordinateur continue de trop chauffer.


## Désactiver la recherche web:
Windows 10:

Dans HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Search crée les clés DWORD suivantes:
|Clé|Valeur|
|:-:|:----:|
|BingSearchEnabled|0|
|AllowSearchToUseLocation|0|
|CortanaConsent|0|

Windows 11:
Dans HKEY_CURRENT_USER\SOFTWARE\Policies\Microsoft\Windows\Explorer créer la clé DWORD ``DisableSearchBoxSuggestions`` et mettre la valeur à 1


## Désactiver les applications en arrière plan

Dans paramètres -> Applications choisir une application puis dans Options avancées désactiver l'activation en arrière plan si elle n'est pas utile

> Le solitaire qui peux se lancer en arrière plan... on peux s'en passer x)

Par défaut Edge se lance également en arrière plan, il faut aller dans les paramètres de Edge pour le désactiver




## Autres optis

Activer la virtualisation pour virtualbox (à copier dans une invite en mode admin):
```sh
bcdedit /set hypervisorlaunchtype off
```
