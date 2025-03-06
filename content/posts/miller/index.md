---
title: "Miller: Un outil pour gérer des csv et des json"
date: 2025-03-06T09:55:59+01:00
draft: false
comment: false
---

Lien officiel: https://github.com/johnkerl/miller

## Afficher un fichier json

```sh
mlr --from <nom du fichier.json> cat
```

## Afficher à l'aide d'un tri

```sh
mlr --from <nom du fichier.json> sort -f <nom de la colonne à trier>
```

## Formatter l'affichage de sortie

Surement l'option la plus utile, on peux afficher du json en csv par exemple:

Pour le cas du json en csv:
```
mlr --j2c --from <nom du fichier.json> cat
```

La commande mlr help flags permet d'afficher un tableau qui regroupe les possibilitées d'affichage de sortie.
Les plus importantes sont:
--j2c : json to csv
--c2j : csv to json
--m2c : markdown to csv
--m2j : markdown to json
--c2m : csv to markdown
--j2m : json to markdown


Convertir un fichier json en csv:
```
mlr --ijson --ojson cat <nom du fichier.json> > <nom du fichier de sortie .csv> 
```


## Sources

https://miller.readthedocs.io/en/latest/10min/
https://miller.readthedocs.io/en/6.13.0/miller-programming-language/
https://miller.readthedocs.io/en/6.13.0/

