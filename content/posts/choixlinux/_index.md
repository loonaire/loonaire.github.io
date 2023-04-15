---
title: "Le choix d'une distribution Linux en 2023"
date: 2023-03-22T09:23:48+01:00
draft: false
comment: false
---

J'écris cet article suite à mon expérience avec différentes distributions linux depuis presque 1an. L'échéance de fin de vie de Windows 10 approche et Windows 11 ne me plais pas, certains des ordinateurs dont je m'occupe sont impactés, ils sont en parfait état de fonctionnement mais Windows 11 ne peux pas être installé dessus, souvent à cause du processeur... Un des autres problèmes que j'ai avec Windows 11 est un retour en arrière en terme de fonctionnalités et une avancée pas toujours très utile sur d'autres fonctionnalité, il consomme également beaucoup trop de ressources. Une de mes expériences avec Windows 11 a été le menu paramètres des mises à jour qui crash... pendant l'installation des mises à jour.

## Mes besoins sur Linux par rapport à Windows

Pour un éventuel passage sur Linux, il me faut un maximum des habitudes que j'ai sur Windows 10, l'OS est bon et plus stable que ces prédécéceurs. L'objectif est de trouver une distribution la plus stable possible suivant les mises à jours tout en gardant les habitudes de Windows. La première est la barre des taches: il me faut la même que Windows 10, un menu pour éteindre / arrêter et les applications, avoir l'heure et la date en bas à droite, un bouton de retour sur le bureau et l'affichage des noms des fenêtres des applications ouvertes. Dans la mesure du possible, il me faut un outil graphique pour l'installation de paquets et les mise à jour ainsi que des paramétrages de l'environnement du bureau ainsi que du système le plus simple possible. Un support d'applications "portables" serait également le bienvenu, il s'agit d'une fonction que j'apprécie beaucoup sous windows (pas besoin d'installer le logiciel). J'ai besoin d'avoir le plus rapidement possible les nouvelles versions des logiciels (surtout pour le dévelopmement en Go/Rust), ce qui exclus d'office certains linux. Il me faut également un support des applications 32bits, ayant besoin de Flash Player 9 sur Linux, la possiblité d'installer des paquets 32bits est important.


## L'environnement de bureau

Je choisi de commencé par le choix de l'environnement de bureau, il conditionne en partie la distribution choisie.  

- Le premier environnement est GNOME, il s'agit de l'environnement choisi par beaucoup de distributions par défaut, c'est le cas de Fedora et d'ubuntu, l'environnement prend beaucoup de consommation de RAM et je trouve son utilisation plutot orientée tactile/mobile, pour quelqu'un habitué à une tablette ou un smartphone cet environnement est intéressant. Cependant j'ai eu quelques problèmes avec GNOME, l'interface des paramètres possède peu de modifications et l'outil GNOME logiciel qui a un plutot orienté flatpak et multi-distribution, il n'utilise pas forcément l'outil de package du système. GNOME utilise la lib GTK et est la base d'autres environnements de bureau.  

- Le second environnement est KDE, il utilise la lib Qt, il s'agit d'un des environnements de bureau le plus utilisé avec GNOME. L'interface est très moderne et proche de Windows 11, interface que je n'aime pas vraiment, jetrouve windows 10 bien mieux. Je n'ai pas vraiment pris le temps de m'intéresser à cette environnement, il est personnalisable et proche de Windows. Coté distribution qui l'utilisent, Manjaro semble favoriser KDE alors qu'ils favorisait XFCE auparavant. KDE permet de monter facilement les volumes au démarrage, chose que tous les environnements de bureau ne peremttent pas. Coté personnel, je trouve le terminal par défaut de KDE trop complexe ainsi que les thèmes des fenêtres trop compliqués.  

- Le troisième environnement, celui qui m'intéresse le plus est XFCE, il est grandement personnalible, léger, simple à utiliser, il peux ressembler à la fois à Windows et à Mac OS avec son dock. Il est basé sur une ancienne version de GNOME, il se base sur GTK ce qui permet d'avoir une compatibilité avec les logiciels qui utilisent GTK. Il ne reçoit pas beaucoup de grosses mise à jours ce qui permet d'avoir une stabilité intéressante par rapport à d'autres environnements de bureau. A ma connaissance, il est surtout utilisé sur les distributions basées sur Arch, Manjaro l'utilise en environnement Officiel, Endeavour OS l'utilise également en environnement par défaut.  

- Cinnamon, un environnement mise en avant avec linux MINT, il s'agit de l'environnement par défaut, en dehors de linux MINT je ne trouve pas que Cinnamon ai un grand interet sur d'autres distributions. Cinnamon est basé sur GNOME 3. Je trouve également problématique le fait d'avoir 2 outils, 1 pour installer des paquets, l'autre pour mettre à jour.  

- Budgie, un environnement très récent basé sur GNOME, il utilise GNOME logiciel pour les mises à jours, que je n'aime pas vraiment. Il est évoqué que l'environnement souhaite changer la lib de base en passant de GTK à EFL ce qui me pose de gros problèmes: risque de casse de fonctionnement de certains logiciels ainsi que l'utilisation d'une librairie graphique spécifique, peu connue et qui manque de mises à jours.  

- Mate, un environnement basé sur GNOME, je ne peux rien dire de plus car je ne le connais pas assez malgré que je l'ai utilisé, il est personnalisable et il a ses particularités.  

Mon choix d'environnement de bureau s'oriente sur XFCE pour sa stabilité, simplicité et personnalisation.  

## les gestionnaires de paquet

Un avis rapide sur les différents gestionnaires de paquet, il est bien de les indiquer au moins pour la connaissance.  

### Les gestionnaires des distributions

- APT: le gestionnaire de Debian et ses dérivées, il est bon mais je le trouve très compliqué lorsque l'on veux apprendre à le maitriser, je le trouve également souvent instable et heureusement que dpkg est la pour le reconfigurer.
- RPM: l'outil de RedHat, il est lié aux distributions sous redhat, je le connais très peu mais il semble très intéressant, stable et efficace.
- Pacman: l'outil des distributions basées sur Arch ainsi que l'outil utilisé par Msys2 sur Windows. Il est plutot simple et documenté ce qui facilite son apprentissage, sa gesiton des paquets est plutot correcte.

### Les gestionnaires universels

- Snap: Un gestionnaire de paquet crée par canonical qui développe Ubuntu, je ne connais pas assez pour en parler
- Flatpak: Un gestionnaire compatible avec tous les linux, chaque paquet intègre ses dépendance, le poids des paquets est plus important mais le paquet fonctionne peu importe le système. Les dépots sont hébergés chez les éditeurs plutot que sur un dépot commun ce qui permet à chaque éditeur de mettre à jour les logiciels lorsqu'il le souhaite et on récupère un fonctionne façon winget de Windows qui marche plutot pas mal.

## Les distributions de base

### Les bases RedHat

Ces versions sont stables, la plupart des distributions sont destinées aux serveurs. Fedora est la seule destinée à de la bureautique cependant elle utilise GNOME. Le suivi des mises à jours est bon et la stabilité des grosses mises à jour semble correct. Sont installation est plutot simple.  
Je ne retiens pas cette distribution à cause de l'utilisation de GNOME.

### Les bases Debian

Il s'agit de distributions avec des paquets testé et stable mais... les versions des logiciels sont anciennes ce qui pose des problème, surtout pour mon coté développeur Go qui reçoit 3/4 mises à jour par an qui peuvent changer beaucoup de choses. Debian intègre également certains éléments à sa manière sans occuper des autres ce qui est un problème. En revanche pour quelqu'un qui souhaite un système stable dans le temps, Debian est très bien.

### Les bases Arch

Une base "standard" qui à l'avantage d'être toujours à jour. Pour avoir testé quelques mois, ca me semble vraiment intéressant et j'ai eu très peu de problème. L'avantage d'arch est qu'il n'y a pas de numéro de version car tous est toujours mis à jour. De plus, l'utilisation des dépots AUR est intéressante, elle augment considérablement le nombre de paquets disponible, idéal pour utiliser des anciennes versions de logiciels.

## Mes choix de distribution

### Linux Mint

Je choisirai cette distribution pour quelqu'un qui souhaite quelque chose de stable et qui ne souhaite pas de problèmes. Mint intègre également beaucoup de logiciels variés tel qu'un lecteur iptv, une suite office, des lecteurs vidéos, la base est très intéressante et l'installation des simple. Les seuls problèmes viennent du menu démarrer un peu compliqué à mon goût ainsi que de la dissociation du menu d'installation de logiciel et de mises à jour. Un autre problème viens du fait qu'il ne faut pas utiliser apt pour mettre à jour mais bien utiliser les outils de Mint au risque de casser le système.

### Manjaro XFCE

J'ai eu plusieurs problème en plusieurs mois avec Manjaro, à chaque fois lié aux clé des dépots de mises à jours, rien de méchant. Le thème de Manjaro à un coté Windows intéressant, et ne ma pas posé de problème. Le système de mises à jour est bon et l'outil de mises à jour Pamac est vraiment génial, je trouve qu'il s'agit du meilleur outils de mises à jour pour linux. La chose qui me pose problème avec Manjaro est que les mises à jours peuvent être lentes à arriver, le kernel 6.2 a mis presque 1mois pour arriver ce qui est beaucoup trop.

## Mon choix final

Mon choix final porte sur Arch avec des éléments de Manjaro, par exemple le thème ainsi que les icones de Manjaro. L'installation d'archlinux est simplifiée depuis l'ajout du script archinstall qui permet d'installer simplement Archlinux. Mon arch aura l'option multilib d'activée pour le support des outils 32bits, je souhaite également ajouter Pamac à mon archlinux car j'aime bien l'interface.
