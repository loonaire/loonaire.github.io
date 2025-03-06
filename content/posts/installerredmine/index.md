---
title: "Installation de redmine en 2025 sur Debian"
date: 2025-03-06T09:54:28+01:00
draft: false
comment: false
---

Comme je ne trouve aucun guide correct, voici un guide pour l'installation de redmine 6 sur debian sans passer par les paquets disponibles dans les dépots de la distribution. Cette méthode permet d'avoir une installation de redmine plus récente que celle proposée par les dépots.


> Lors de l'installation de Debian je ne saisi pas de mot de passe pour root, il faut utiliser sudo 


> J'utilise vi comme éditeur, si vous préférez nano, remplacez vi dans les commandes par nano


> Mon installation utilisera postgresql pour la base de données, il s'agit actuellement de l'outil le plus mis en avant par l'équipe de redmine, une installation sous mysql ou mariadb est également possible


> Je laisse également debian installer les versions par défaut de postgresql et ruby, cela permet de simplifier les mises à jour


> Je part du principe que nous installerons un environnement de production

## Préparation

Pour commencer, il faut une machine sous Debian 12 installée et mise à jour. Pour les ressources, 1 processeur et 1Go de ram suffisent.

Installation des paquets qui vont être nécessaires sous debian:
```sh 
sudo apt install apache2 postgresql ruby imagemagick ruby-dev make patch libxslt1-dev libxml2-dev libpq-dev libcurl4-openssl-dev zlib1g-dev gcc g++ libyaml-dev libapache2-mod-passenger
```

> Pour les autres distributions (redhat) il faut adapter le nom des paquets


### Préparation de la base de données de postgresql

Se connecter au serveur postgre avec la commande:

```sh
sudo su - postgres
```

Création du compte qui servira à la connexion au serveur et de la base de données
> Pensez à changer my_password par un vrai mot de passe
```sh
createuser redmine 
createdb redmine -O redmine 
psql -c "alter user redmine with password 'mypassword'" 
```

Sortir du shell de l'utilisateur postgresql avec la commande ``exit``


### Optionnel: Installer passenger si libapache2-mod-passenger n'est pas installé

Installation puis configuration de passenger:
```sh
gem install passenger
```

```sh
passenger-install-apache2-module 
```

Modifier la configuration d'apache pour ajouter le chargement du module passenger:
```sh
vim /etc/apache2/conf-available/passenger.conf 
```


```sh
   LoadModule passenger_module /var/lib/gems/3.1.0/gems/passenger-6.0.18/buildout/apache2/mod_passenger.so
   <IfModule mod_passenger.c>
     PassengerRoot /var/lib/gems/3.1.0/gems/passenger-6.0.18
     PassengerDefaultRuby /usr/bin/ruby3.1
   </IfModule>
```

Recharger Apache2:
```sh
a2enconf passenger
systemctl restart apache2
```


## Installation 

> Ici je fait toutes les manipulations en tant que root pour plus de facilité et éviter les sudo, avec sudo il faut utiliser la commande sudo -i

Télécharger la dernière version de redmine (le lien n'est à ajout, à vous de le récupérer [ici](https://www.redmine.org/projects/redmine/wiki/Download)):
```sh
wget https://www.redmine.org/releases/redmine-6.0.1.tar.gz -O /tmp/redmine.tar.gz
```

On crée le dossier qui acceuillera redmine
```sh
mkdir /var/www/redmine
```

On extrait l'archive et on déplace le contenu extrait à l'emplacement d'installation de redmine:
```sh
tar -xzf /tmp/redmine.tar.gz -C /var/www/redmine --strip-components=1
```

### Préparation des dossiers supplémentaires

Pour le coté pratique en cas de mise à jour et le coté sécuritaire, certains dossiers de redmine seront déplacé et ne seront pas utilisés à leurs emplacements initiaux:

```sh
mkdir -p /var/lib/redmine/{files,config,tmp}
mkdir -p /var/log/redmine
```


### Paramétrages des fichiers de configration

> Pour l'utilisation d'un autre sgdb, les configurations d'exemple sont disponibles dans le fichier config/database.yml.example de redmine

Fichier de configuration pour l'installation de redmine avec postgresql:
```sh
cat <<EOF > /var/www/redmine/config/database.yml
production:
  adapter: postgresql
  database: redmine
  host: localhost
  username: redmine
  password: "mypassword"
EOF
```

### Création de la configration 

On commence par copier le fichier d'exemple de la configuration:
```sh
cp /var/www/redmine/config/configuration.yml.example /var/www/redmine/config/configuration.yml
```

#### Mise en place de l'envoi de mails pour les notifications

Je n'utilise pas de serveur de mail pour ce guide donc les informations seront approximative

Chercher dans le nouveau fichier configration.yml la partie ``email_delivery`` et copier la configuration à utiliser sous la ligne ``email_delivery:``

#### Gérer l'emplacement des pièces jointes

Par sécurité, on n'utilisera pas le dossier files par défaut de redmine, les pièces jointes seront stockées dans /var/lib/redmine/files, ceci permet de garder les pièces jointes en cas de montée de version de redmine:
Ligne 74: attachments_storage_path:
remplacer par: attachments_storage_path: /var/lib/redmine/files
cela peux être fait en une commande:
```sh
sed -i -e "s|^  attachments_storage_path:$|  attachments_storage_path: /var/lib/redmine/files|" /var/www/redmine/config/configuration.yml
```


#### Déplacement des dossiers et création des liens symboliques

Pour simplifier les mises à jours, on va déplacer/supprimer des dossiers pour ne pas avoir à copier coller à chaque mise à jour:

```sh
rm -r /var/www/redmine/log
mv /var/www/redmine/config/configuration.yml /var/lib/redmine/config/
mv /var/www/redmine/config/database.yml /var/lib/redmine/config/
ln -s /var/lib/redmine/config/configuration.yml /var/www/redmine/config/configuration.yml
ln -s /var/lib/redmine/config/database.yml /var/www/redmine/config/database.yml
ln -s /var/log/redmine /var/www/redmine/log 
```

#### Gestion des droits du site

Pour terminer avant de passer à l'installation, on donne les droits à www-data sur tous les fichiers de redmine:
```sh
chown -R www-data:www-data /var/www/redmine/
```

On change le propriétaire des autres dossiers:
```sh
chown -R www-data:www-data /var/lib/redmine 
chown -R www-data:www-data /var/log/redmine
```

### Installation de ruby

On se place dans notre installation de redmine:
```sh
cd /var/www/redmine
```

On installe gem:
```sh
gem install bundler 
```

On prépare la configuration de bundle puis on télécharge les dépendances:

> Si vous n'utilisez pas postgresql, retirez le nom du serveur de base de données de la commande et ajoutez postgresql

```sh
bundle config set --local without 'development test mysql sqlite' 
```

On lance l'installation:
```sh
bundle install
```

> Ici je fait mon installation en tant que root mais normalement il faut le faire depuis un utilisateur classique 

> Attention: si la commande bundle install échoue, corriger l'erreur puis relancer la commande, souvent le problème viens du manque de dépendances

On génère le secret de l'installation:
```sh
bundle exec rake generate_secret_token
```

On génère la base de données:
```sh
bundle exec rake db:migrate RAILS_ENV=production
```

Créer la configuration apache du site

```sh
cat <<EOF > /etc/apache2/sites-available/redmine.conf
<VirtualHost *:80>
    ServerName redmine.tld
    DocumentRoot /var/www/redmine/public
</VirtualHost>

<Directory "/var/www/redmine/public">
    Options FollowSymLinks
    AllowOverride All
</Directory>
EOF
```

activer le site:
```sh
a2dissite 000-default.conf
a2ensite redmine.conf
```

On donne les droits à www-data sur le dossier:
```sh
chown -R www-data:www-data /var/www/redmine 
```

Redémarrer le serveur apache:
```sh
systemctl reload apache2
```


On arrive sur la page d'accueil, les identifiants pour la connexion sont admin / admin

## Exploitation

Redmine est installé mais il reste quelques étapes utiles pour éviter les problèmes futur

### Logrotate

crée le fichier logrotate pour redmine:
> Note: Redmine log beaucoup, ici je met logrotates à weekly mais il peux être passé à daily

> La durée de stockage pour les logs doit être d'au moins 6mois, ce qui explique le weekly / rotate 26 (+/- 6mois), en daily le rotate serait de 180 

```sh
cat <<EOF > /etc/logrotate.d/redmine
/var/log/redmine/*.log {
  weekly
  dateext
  dateformat .%Y-%m-%d
  missingok
  rotate 26
  compress
  delaycompress
  notifempty
  copytruncate
}
EOF
```

### Mises à jour

On commence par arrêter le serveur web et backup l'installation actuelle (à lancer en tant que root):
```sh
systemctl stop apache2
tar -czvf ~/redmine-backup.tar.gz -C /var/www redmine
mv /var/www/redmine /var/www/redmine-old
mkdir /var/www/redmine
wget https://www.redmine.org/releases/redmine-6.0.3.tar.gz -O /tmp/redmine.tar.gz
tar -xzf /tmp/redmine.tar.gz -C /var/www/redmine --strip-components=1
cp -r /var/www/redmine-old/plugins /var/www/redmine/plugins
rm -r /var/www/redmine-old
```

Recréer les liens:
```sh
rm -r /var/www/redmine/log
ln -s /var/lib/redmine/config/configuration.yml /var/www/redmine/config/configuration.yml
ln -s /var/lib/redmine/config/database.yml /var/www/redmine/config/database.yml
ln -s /var/log/redmine /var/www/redmine/log
```

On donne les bon droits:
```sh
chown -R www-data:www-data /var/www/redmine
```



On se place dans notre installation:
```sh
cd /var/www/redmine
```

Configuration de Bundle:
```sh
bundle config set --local without 'development test mysql sqlite' 
bundle install
```

Regénération du secret:
```sh
bundle exec rake generate_secret_token
```

Migration de la base de données:
```sh
bundle exec rake db:migrate RAILS_ENV=production 
```
Si des plugins sont installés:
```sh
bundle exec rake redmine:plugins:migrate RAILS_ENV=production
```

Nettoyage du cache:
```sh
bundle exec rake tmp:cache:clear RAILS_ENV=production
```

Relancer le serveur apache:
```sh
systemctl start apache2
```


## Sources

- [Guide d'installation](https://www.server-world.info/en/note?os=Debian_12&p=redmine)
- [Installer redmine avec mysql](https://reintech.io/blog/installing-configuring-redmine-debian-12)
- [Guide officiel](https://www.redmine.org/projects/redmine/wiki/RedmineInstall)
- [Guide d'installation du wiki debian (paquets officiel debian)](https://wiki.debian.org/Redmine)