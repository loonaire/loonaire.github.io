---
title: "Déployer Windows 10 Sur une Surface Go 4"
date: 2024-01-27T17:45:58+01:00
draft: false
comment: false
---

Il y a peu j'ai du déployer Windows 10 sur des Surface Go 4, de manière générale Windows 10 sur les Surface Go 4 c'est compliqué, et au dela des Surface Go 4, cet article s'étend aux appareils qui utilisent la mémoire UFS.
s

## Le fonctionnement du MDT

Avant de commencer, un rapide brief sur le MDT, il se compose de 3 composants distincts:
    - Le serveur WDS qui est le serveur qui acceptera les connexions et qui va contenir l'image de démarrage pour le déploiement
    - L'outil MDT qui est la console Deployment Workbench qui permet de générer l'image de démarrage du déploiement ainsi que la séquence de tâches à executer, les fichiers résultants sont stockés dans un répertoire partagés
    - Le composant ADK + le module ADK winPE (à partir de windows 10 de 2019) qui sont les fichiers qui permettent la génération d'image de boot winPE

Comment ca marche?

Le MDT crée un dossier partagé, la console Deployment Workbench permet de gérer ce dossier, ici sera importé ce que l'on souhaitera installer et faire, il faut importer les drivers, l'os et les logiciels à installer. Une séquence de tâche liée au contenu du partage sera mise en place.  

Une fois en place, il faut générer une image de déploiement dans la console, cette génération va faire appel aux fichier installés par l'ADK, une image de démarrage liteTouch sera crée.  

Enfin, sur le serveur WDS, il faut importer l'image liteTouch de démarrage, ensuite sur les ordinateurs client, il faut démarrer en PXE et l'image de démarrage se chargera automatiquement et utilisera la séquence de tâche présente dans le dossier de partage du MDT. A noter que la modification de la séquence de tâches ainsi que l'ajout de contenu dans le partage du MDT n'implique pas forcément une mise à jour de l'image de démarrage.


## Windows sur les Surface Go 4

Ces tablettes sont prévues pour Windows 11 en priorité mais dans mon cas, Windows 10 et 11 sont dans le même bâteau. Ces Surface utilisent de la mémoire UFS et le pilote de cette mémoire n'est pas fourni avec le package de driver de la tablette, en plus seules les iso de Windows 10 et 11 à partir de la version 22H2 de octobre 2023 intègrent ce driver.

## Le déploiement

### Les causes

Le déploiement via MDT sur les Surfaces Go 4 viens du pilote du stockage de l'image de boot winPE, la plupart du temps les images winPE d'installation ne sont jamais mises à jour car le Windows ADK n'est pas mis à jour. De plus, la dernière version de windows ADK à supporter le 32bit est la version 2004.

### La solution

La solution est de mettre à jour le serveur et le kit ADK + mise en place d'un iso avec une image d'octobre 2023 au minimum + regénération de l'image de démarrage. La solution est disponible dans les parties suivantes

### Le serveur WDS 

Avant de commencer les modifications, le serveur WDS doit être fonctionnel, si il n'est pas fonctionnel et indiquer en rouge, il faut peux être le réinitialiser avec les commandes ([source](https://www.raytechnote.com/wds-server-not-working-after-in-place-upgrade-from-windows-2012-to-windows-2019/)):

```batch
wdsutil /uninitialize-server 
wdsutil /Initialize-Server /RemInst:C:\RemoteInstall 
```

Puis passer l'écoute AnswerClients via set-server en ALL ou Known:
```
wdsutil /Set-Server /AnswerClients:All
```

### L'ADK

L'ADK pour le déploiement d'images Windows est un sujet qui deviens compliqué: sur les versions sorties après la version 2004 l'ADK à beaucoup de bug.  
La meilleure solution est à mon avis de mettre à jour l'ADK sur la version 22H2, si vous n'avez pas besoin de déployer du 32bits il s'agit de la solution la plus simple. A noter également que la version 2023 possède plusieurs bug, un premier concerne l'absence de VBSCRIPT en fonctionnalité (voir détails plus bas) et un autre provienais de ZTIDrivers lors de mes tests, il s'agit de la version avec le plus de bug.

#### Mettre à jour l'ADK vers la version 22H2

Pour mettre à jour l'ADK vers la version 22H2, il faut désinstaller l'ancienne version et installer la nouvelle qui est disponible sur [cette page du support Microsoft](https://learn.microsoft.com/en-us/windows-hardware/get-started/adk-install#other-adk-downloads). A l'issu de cette installation, il faudra regénérer complètement l'image de démarrage dans la console DeploymentWorkbench.  

Une fois fait, il faut regénérer une image de démarrage dans la console MDT et l'importer dans les images de démarrage du server WDS

#### Corriger le bug des propriétés de winPE dans la console Deployment Workbench

Ce bug est genant de viens du retrait de l'architecture 32bits dans l'ADK Windows, il est présent à partir des version suivants la version 2004 de l'ADK.

Pour le corriger, il faut créer le dossier `C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Windows Preinstallation Environment\x86\WinPE_OCs` dans les dossiers de l'ADK, voici une commande pour le faire automatiquement:

```batch
mkdir C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Windows Preinstallation Environment\x86\WinPE_OCs
```

[Source de la résolution du problème](https://learn.microsoft.com/en-us/answers/questions/758014/deployment-workbench-crashes-when-opening-the-win)

#### Corriger les bugs de l'ADK 22H2

##### Le problème du script qui ne répond pas

L'ADK 22H2 provoquera un problème au moment de saisir les paramètres de déploiement, il y aura des fenêtres indiquant qu'il y a un problème lors de l'execution du script. 
La correction est simple et fournie par Microsoft [ici](https://learn.microsoft.com/fr-fr/mem/configmgr/mdt/known-issues#hta-applications-report-script-error-after-upgrading-to-adk-for-windows-11-version-22h2)

Il faut modifier le fichier `C:\Program Files\Microsoft Deployment Toolkit\Templates\Unattend_PE_x64.xml` et remplacer son contenu par:
```xml
<unattend xmlns="urn:schemas-microsoft-com:unattend">
    <settings pass="windowsPE">
        <component name="Microsoft-Windows-Setup" processorArchitecture="amd64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State">
            <Display>
                <ColorDepth>32</ColorDepth>
                <HorizontalResolution>1024</HorizontalResolution>
                <RefreshRate>60</RefreshRate>
                <VerticalResolution>768</VerticalResolution>
            </Display>
            <RunSynchronous>
                <RunSynchronousCommand wcm:action="add">
                    <Description>Lite Touch PE</Description>
                    <Order>1</Order>
                    <Path>reg.exe add "HKLM\Software\Microsoft\Internet Explorer\Main" /t REG_DWORD /v JscriptReplacement /d 0 /f</Path>
                </RunSynchronousCommand>
                <RunSynchronousCommand wcm:action="add">
                    <Description>Lite Touch PE</Description>
                    <Order>2</Order>
                    <Path>wscript.exe X:\Deploy\Scripts\LiteTouch.wsf</Path>
                </RunSynchronousCommand>
            </RunSynchronous>
        </component>
    </settings>
</unattend>
```

**Il faut ensuite regénérer complètement l'image et l'importer dans les images de démarrage du WDS**

### L'image d'installation

Cette partie pose moins de problème et est plus classique. Il faut utiliser une image iso de Windows qui contient une version supérieure ou égale à celle décembre 2023.  
Cette image est disponible sur le site de Microsoft.

#### La commande DISM pour vérifier les images

On vérifie à l'iso monté en cd virtuel, la lettre du lecteur est à adapter en fonction de la situation:
```batch
dism /Get-WimInfo /WimFile:D:/sources/install.esd
```
Cette commande va afficher toutes les images d'installation disponibles dans l'image iso.

#### Convertir le fichier install.esd en install.wim

A partir de la partie précédente, il faut éxecuter la commande (adapter les chemins en fonction de la situation):
```batch
dism /export-image /SourceImageFile:d:/sources/install.esd /SourceIndex:1 /DestinationImageFile:c:\install.wim /Compress:max /CheckIntegrity
```

[Lien vers la source](https://www.it-connect.fr/wds-convertir-un-fichier-esd-en-wim/)


#### Import de l'image dans le MDT

Il faut enfin importer l'image dans le MDT via la console Deployment Workbench et modifier la séquence de tâches pour pointer vers le nouvel iso.


#### Les problèmes avec LAPS

Un problème qui a été découvert, si un appareil est déployé avec un iso qui contient une version de Windows 10 qui date de fin 2023, le mot de passe administrateur peux poser des problème, la solution est de ne pas modifier les ordinateurs dans l'AD lors du déploiement ou de mettre à jour les GPO de LAPS.

## Les autres solutions pour l'ADK

Il existe d'autres moyens qui semblent marcher cependant ils peuvent prendre beaucoup de temps et être délicat à mettre en place:

- La première consiste à garder un ADK en version 2004 et à appliquer les mises à jour cumulatives à winPE pour avoir un winPE 2004 avec les mises à jour de Windows 10 22H2. Cette solution n'a pas marché pour moi à cause d'erreur lors du patch du winPE, quelques sources:  
    - [lien 1 (guide de mise à jour)](https://www.niallbrady.com/2023/11/23/problems-imaging-a-surface-go-4-using-configuration-manager/)
    - [lien 2 (doc microsoft sur les images de démarrage)](https://learn.microsoft.com/fr-fr/windows/deployment/customize-boot-image?tabs=powershell#updating-the-boot-image-and-boot-media-in-mdt)
    - [lien 3 (guide de mise à jour)](https://www.windows-noob.com/forums/topic/23351-problems-imaging-a-surface-go-4-using-configuration-manager/)

- Une autre solution est de mettre à jour vers la dernière version de l'ADK. Cette version est technique à mettre en place:
    - Ce problème est crée à cause du retrait de la fonctionnalité VBSCRIPT dans l'installation de Windows
    - Il faut récupérer dans une version Windows 11 insider preview (au moment de la publication originale de cette article, janvier 2024) les fichiers .cab de la fonctionnalité. Cette étape est très complexe, l'installation doit être en 50 et 70% pour permettre la récupération des fichier et il faut le faire de préférence en VM ou en machine qui peux être redéployée.
        Voici les commandes à utiliser:
        - Vérifier si la fonctionnalité est disponible avec la commande: 
        ```
        DISM /online /get-capabilities
        ```
        Il faut trouver la fonctionnalité: VBSCRIPT~~~~

        - Ensuite, désinstaller la fonctionnalité:
        ```
        DISM /online /remove-capability /capabilityname:VBSCRIPT~~~~
        ```
        - On réinstalle ensuite la fonctionnalité:
        DISM /online /add-capability /capabilityname:VBSCRIPT~~~~

    - L'explication de la manipulation à faire est disponible [ici](https://www.deploymentresearch.com/fixing-vbscript-support-in-windows-adk-sep-2023-update-build-25398/)

