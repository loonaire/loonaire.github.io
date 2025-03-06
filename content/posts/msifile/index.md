---
title: "Manipulation de fichier msi avec powershell"
date: 2025-03-06T09:57:07+01:00
draft: false
comment: false
---

## Installer un fichier MSI en ligne de commande

Il faut utiliser la commande:
msiexec /i <fichier msi>

Pour une installation silencieuse:
msiexec /i <fichier msi> /qn 

## Récupération d'information sur un fichier msi

### Obtenir les variables possibles pour l'installation d'un programme

```sh
msiexec /package <fichier .msi> /lp! properties.txt
```

Cette commande va créer un fichier properties.txt dans lequel seront affichées les propriétés utilisées pour l'isntallation du fichier,
il contient le nom du logiciel, sa version, son éditeur, et d'autres éléments utilisés par Windows, il contient également une chose importante: les variables utilisable lors de l'installation du logiciel.
Nous pourrons ainsi personnalisé l'installation du logiciel en utilisant une commande ressemblant à la suivante:
```sh
msiexec /package <fichier .msi> <nom Propriété>=<valeur> 
```

Les propriétés sont séparées par des valeurs et il n'y a pas d'espace de chaque coté du signe =

## Obtenir ces éléments en Powershell

L'élément à l'origine de cet article.

```powershell
function Get-MSIProperties {
    <#
    .SYNOPSIS
    Function to get properties from MSI file
    
    .DESCRIPTION
    Function to get every properties of MSI file.
    Return simple MSI properties like ProductName, ProductVersion, ProductLanguage
    Custom properties for installation are return in SecureCustomProperties Member 
    
    .PARAMETER Path
    Specifies the full path to MSI file.
    
    .EXAMPLE
    PS> Get-MSIProperties file.msi
    PS> Get-MSIProperties -Path file.msi
    
    .NOTES
    Inspired from https://winadminnotes.wordpress.com/2010/04/05/accessing-msi-file-as-a-database/
    #>
    param (
        [Parameter(Mandatory = $true, Position = 0, HelpMessage = 'Specifies path to MSI file.')][ValidateScript({
        if ($_.EndsWith('.msi')) {
            $true
        } else {
            throw ("{0} must be an '*.msi' file." -f $_)
        }
        })]
        [String] $Path
    )
    
    # Avoid error with the opening of msi database
    $Path = (Get-ChildItem -Path "$($Path)").FullName
    
    if (-not (Test-Path -Path $Path)) {
        Write-Error "Path is not valid $Path"
        return $Null
    }

    $MsiPropertiesObject = New-Object PSObject
    $WindowsInstaller = New-Object -ComObject WindowsInstaller.Installer
    try {
        $Database = $WindowsInstaller.GetType().InvokeMember("OpenDatabase", "InvokeMethod", $Null, $WindowsInstaller, @($Path,0))
        $View = $Database.GetType().InvokeMember("OpenView", "InvokeMethod", $Null, $Database, ("SELECT * FROM Property"))
        $View.GetType().InvokeMember("Execute", "InvokeMethod", $Null, $View, $Null)
        do {
            $Record = $View.GetType().InvokeMember("Fetch", "InvokeMethod", $Null, $View, $Null)
            if ($Null -ne $Record) {
                $PropertyName = $Record.GetType().InvokeMember("StringData", "GetProperty", $Null, $Record, 1)
                $PropertyValue = $Record.GetType().InvokeMember("StringData", "GetProperty", $Null, $Record, 2)
                $MsiPropertiesObject | Add-Member -MemberType NoteProperty -Name "$PropertyName" -Value "$PropertyValue"
            }

        } while ($Null -ne $Record)
        $View.GetType().InvokeMember('Close', "InvokeMethod", $Null, $View, $Null)  
    } catch {
        throw "Error on loading database of MSI file $_"
    }
    return $MsiPropertiesObject
}
```

## Sources

[](https://winaero.com/install-a-winget-app-with-custom-arguments-and-command-line-switches/)
[](https://stackoverflow.com/questions/72047380/powershell-get-properties-of-msi)
[](https://github.com/itpro-tips/PowerShell-Toolbox/blob/master/Get-MSIFileInformation.ps1)
[](https://winadminnotes.wordpress.com/2010/04/05/accessing-msi-file-as-a-database/)
[](https://powershellexplained.com/2016-10-21-powershell-installing-msi-files/)

