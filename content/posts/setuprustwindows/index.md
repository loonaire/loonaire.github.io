---
title: "Développer en Rust sur Windows avec VSCode et sans msvc"
date: 2023-01-14T20:50:48+01:00
draft: false
comment: false
---

Souhaitant retenter d'apprendre Rust, j'ai dû commencer par mettre en place mon environnement de développement. J'avais déja essayé Rust mais en utilisant ce qui étais recommandé sur le site officiel de Rust: utiliser rustup version windows et le compilateur msvc, cette version marche très bien mais a le problème d'avoir besoin du SDK Windows d'installer sur l'ordinateur et entre les outils qui ne me servirai que pour Rust et qui ont besoin d'environ 5Go d'espace, je trouve ca beaucoup trop, surtout que j'ai déja un environnement de compilation prêt grâce à MSYS2.

Ma solution utilise donc MSYS2 pour fonctionner, avant de continuer je précise que j'ai surtout besoin d'une toolchain Rust pour compiler seulement pour ma machine, je n'ai pas pour le moment besoin de compiler en release pour d'autres machines, si vous souhaitez avoir un environnement très stable et simplement fonctionnel, utilisez la solution officielle disponible sur le site de Rust.  

Comme logiciel prérequis il faut VSCode ou un de ses forks, l'extension Rust officielle pour VSCode Rust-analyzer ([lien](https://rust-analyzer.github.io/)) et le logiciel MSYS2 ([lien](https://www.msys2.org/)) qui permet d'avoir accès a des commandes linux et des outils de compilations libres sous windows. MSYS2 permet également de mettre simplement à jour les outils qu'il installe grâce au gestionnaire de paquet pacman qui est celui d'archlinux.  


> Pour la suite, j'utilise le dépot mingw64 de MSYS2, si vous souhaitez utiliser un autre dépot, vous devrez modifier mingw64 par le nom du dépot souhaité, j'utilise également l'emplacement d'installation par défaut de MSYS2 qui est C:.  

Pour commencer, sur MSYS2, on commence par mettre à jour la base de données des paquets et on installe les paquets nécessaires:
```sh
pacman -Syyuu
pacman -S mingw-w64-x86_64-toolchain
pacman -S build-devel
pacman -S mingw-w64-x86_64-rust 
```

Ensuite, sur VSCode il faut isntaller l'extension Rust-analyzer si elle n'est pas installée et ouvrir le fichier utilisateur settings.json à l'aide du raccourci ***ctrl+maj+p*** puis taper ***settings.json*** et choisir ***Open User Settings (JSON)***. Il faudra ajouter les lignes suivantes avant la dernière accolade du fichier:  
Pour ajotuer le terminal MSYS2 dans VSCode:  
```json
    "terminal.integrated.profiles.windows": {
        "MSYS2 Bash": {
            "path": "C:\\msys64\\usr\\bin\\bash.exe",
            "args": ["--login", "-i"],
            "env": {
                "MSYSTEM": "MINGW64",
                "CHERE_INVOKING": "1",
                "MSYS2_PATH_TYPE": "inherit"
            }
          }
    },  
```  
Pour rendre fonctionnelle l'extension rust-analyzer avec notre installation de rust, il faut ajouter les lignes suivantes (toujours avant la dernière accolade du fichier):  
```json
    "rust-analyzer.server.path": "C:\\msys64\\mingw64\\bin\\rust-analyzer.exe",
    "rust-analyzer.checkOnSave.enable" : true,
    "rust-analyzer.checkOnSave.features" : "all",
```

Désormais, il sera possible d'ouvrir depuis VSCode un terminal MSYS2 et rust-analyzer sera fonctionnelle dans VSCode avec MSYS2 et sans avoir besoin des outils Visual Studio Build Tools. A titre d'information, mon installation de MSYS2 pèse environ 2.7Go et est totalement indépendante du système.  
