---
title: "Retour Experience Laptop de 2021"
date: 2022-06-26T20:00:47+02:00
draft: false
comment: false
---

J'ai récemment fait l'acquisition d'un nouvel ordinateur portable, il s'agit d'un Asus TUF706HM, coté composant, il possède un processeur intel core i5 11400h avec une carte graphique nvidia RTX3060 Max-q.
Le chassis est en plastique complet et en pleine charge le ventilateur est très bruyant et s'entend de loin. Ce problème semble commun aux processeurs intel core iX depuis la gen 10, il semble également un peu présent sur les ordinateurs avec des processeurs AMD Ryzen 5xxxx, en charge le processeur et la carte graphique montent beaucoup en température. Cette température tourne autour de 90-100°C pour le processeur et la carte graphique arrive facilement à 80-90°C, c'est clairement trop et malheureusement le seul moyen de limiter cette température est de brider le processeur, ce qui permet de gagner quelques degrés.

- La première solution consiste à utiliser les logiciels du fabricant qui permettent généralement de controler la vitesse du ventilateur en fonction de la température des composants, cette solution est simple à mettre en oeuvre cependant je ne l'aime pas, il faut savoir ce que l'on fait, tout les fabricant ne permettent pas une gestion convenable des ventilateurs et souvent il y a de la télémétrie cachée...

- La seconde manipulation consiste à limiter la tension du CPU (undervolting), pour ce faire il faut aller dans le bios et régler la valeur à -100mV, dans mon cas j'ai limité à -30mV car le bios ne me permet pas de descendre plus bas. Cette solution permet déja de limiter un peu la température du CPU et de gagner quelques degrés.

Je me suis également rendu compte que Windows n'intègre plus toutes les options dans les options d'alimentation, par exemple sur certains anciens ordinateurs on peux limite la fréquence du CPU, cette manipulation permet également de gagner en température et donc en bruit de ventilateur. Ces options sont réactivables en passant par le registre, voici un script powershell qui permet d'en réactiver une partie:

```powershell
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\a1841308-3541-4fab-bc81-f71556f20b4a\" -Name "Attributes" -Value "2"

Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\8c5e7fda-e8bf-4a96-9a85-a6e23a8c635c\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\381b4222-f694-41f0-9685-ff5bb260df2e\" -Name "Attributes" -Value "2"

Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\e73a048d-bf27-4f12-9731-8b2076e8891f\637ea02f-bbcb-4015-8e2c-a1c7b9c0b546\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\e73a048d-bf27-4f12-9731-8b2076e8891f\d8742dcb-3e6a-4b3c-b3fe-374623cdcf06\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\e73a048d-bf27-4f12-9731-8b2076e8891f\5dbb7c9f-38e9-40d2-9749-4f8a0e9f640f\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\e73a048d-bf27-4f12-9731-8b2076e8891f\bcded951-187b-4d05-bccc-f7e51960c258\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\e73a048d-bf27-4f12-9731-8b2076e8891f\9a66d8d7-4ff7-4ef9-b5a2-5a326ca2a469\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\e73a048d-bf27-4f12-9731-8b2076e8891f\8183ba9a-e910-48da-8769-14ae6dc1170a\" -Name "Attributes" -Value "2"

Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\4f971e89-eebd-4455-a8de-9e59040e7347\a7066653-8d6c-40a8-910e-a1f54b84c7e5\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\0012ee47-9041-4b5d-9b77-535fba8b1442\6738e2c4-e8a5-4a42-b16a-e040e769756e\" -Name "Attributes" -Value "2"

Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\5fb4938d-1ee8-4b0f-9a3c-5036b0ab995c\" -Name "Attributes" -Value "2"

Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\48672f38-7a9a-4bb2-8bf8-3d85be19de4e\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\2e601130-5351-4d9d-8e04-252966bad054\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\fea3413e-7e05-4911-9a71-700331f1c294\" -Name "Attributes" -Value "2"

Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\501a4d13-42af-4429-9fd1-a8218c268e20\ee12f906-d277-404b-b6da-e5fa1a576df5\" -Name "Attributes" -Value "2"

Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\8619b916-e004-4dd8-9b66-dae86f806698\" -Name "Attributes" -Value "2"

Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\54533251-82be-4824-96c1-47b60b740d00\75b0ae3f-bce0-45a7-8c89-c9611c25e100\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\54533251-82be-4824-96c1-47b60b740d00\bc5038f7-23e0-4960-96da-33abaf5935ec\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\54533251-82be-4824-96c1-47b60b740d00\893dee8e-2bef-41e0-89c6-b55d0929964c\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\54533251-82be-4824-96c1-47b60b740d00\94d3a615-a899-4ac5-ae2b-e4d8f634367f\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\54533251-82be-4824-96c1-47b60b740d00\be337238-0d82-4146-a960-4f3749d470c7\" -Name "Attributes" -Value "2"


Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\238c9fa8-0aad-41ed-83f4-97be242c8f20\9d7815a6-7ee4-497e-8888-515a05f02364\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\238c9fa8-0aad-41ed-83f4-97be242c8f20\94ac6d29-73ce-41a6-809f-6363ba21b47e\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\238c9fa8-0aad-41ed-83f4-97be242c8f20\bd3b718a-0680-4d9d-8ab2-e1d2b4ac806d\" -Name "Attributes" -Value "2"
Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\238c9fa8-0aad-41ed-83f4-97be242c8f20\29f6c1db-86da-48c5-9fdb-f2b67b1f44da\" -Name "Attributes" -Value "2"

Set-ItemProperty -path "HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\fbd9aa66-9553-4097-ba44-ed6e9d65eab8\3c0bc021-c8a8-4e07-a973-6b14cbcb2b7e\" -Name "Attributes" -Value "2"

Set-ItemProperty -path "HKML:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings\19cbb8fa-5279-450e-9fac-8a3d5fedd0c1\12bbebe6-58d6-4636-95bb-3217ef867c1a\" -Name "Attributes" -value "2"

```

Pour réactiver seulement la valeur qui permet de limiter la fréquence du processeur, la manipulation est ici: [lien vers majorgeek](https://www.majorgeeks.com/content/page/how_to_add_or_remove_maximum_processor_frequency.html).

Pour plus d'infos sur quels options sont réactivables [lien vers tenforum qui regroupe toutes les possiblitées](https://www.tenforums.com/general-support/153908-didnt-meta-advanced-power-options-regfile-exist-one-point.html), la commande powercfg est également intéressante pour modifier les profils d'alimentation en ligne de commande.

Après avoir réactiver ces options, en allant dans les options d'alimentation avancées, on peu régler la fréquence maximale du CPU, régler cette fréquance plus basse que celle de la fréquence max du CPU permet de gagner quelques degrés. La meilleure solution est de faire des tests, dans mon cas le core i5 11400H est limité à 3800Mhz, après tests je perd environ 20 fps dans les jeux cependant l'ordinateur gagne environ 5°C.

Enfin, une autre solution semble consister à bloquer l'hyperthreading dans Windows, pour cela dans les options d'alimentations avancées, il faut désactiver l'option Mode d'amélioration des performances de processeur.
