### TP 01 Installation Serveurs
Pour ce TP, j’ai installé VirtualBox version 7.2.4 sur mon ordinateur personnel. Mon PC est un Mac Intel, donc je n’ai pas eu de problèmes de compatibilité avec VirtualBox.

L’installation se fait avec une image NetBoot. J’ai téléchargé l’image mini.iso depuis le chemin suivant :
http://ftp.fr.debian.org/debian/dists/bookworm/main/installer-amd64/current/images/netboot/mini.iso

J’ai choisi un miroir Debian situé en France, pour une meilleure vitesse de téléchargement.
J’ai choisi la release bookworm (Debian 12) car il s’agit d’une version stable de Debian.
L’architecture amd64 correspond au type de processeur utilisé par ma machine virtuelle VirtualBox.

Pour le fichier mini.iso, le dernier update est du 05-Jan-2026 et la taille est 62 Mo

Dans la phase d’installation de Debian :
pour le miroir Debian, je suis resté sur un miroir en France
pour les locales : fr_FR.UTF-8 et ajout de en_US.UTF-8
clavier : français
Mais je rencontre des problèmes de touches sur le clavier du MacBook : apparemment, l’installation utilise un clavier “standard” (comme sur Windows), donc certaines touches ne correspondent pas exactement.

ps : j'ai pas reussi a  maitre le Bootable flag ON dans partition racine dans "la racine".

ps2 : J’ai créé une deuxième VM (server2), parce que sur la première, j’ai installé une interface graphique (Debian Desktop Environment).

----------------------------------------------------------------

### La Connection: 

pour Trouver l’adresse IP de la VM - dans Debian: ip a
Commande sur le pc : ssh root@192.168.1.59
j’entre le mot de passe root que j’ai défini dans la config.
connexion réussie, j’arrive sur le shell Debian avec :

root@server2:~#

----

### Space Usage : 

la comande df -h (disk free -h) affiche l’utilisation du système de fichiers sur la machine Linux. L’option -h permet d’avoir un affichage plus facile à lire.

La racine / 
Taille totale : 9.1 Go
Espace utilisé : 1.2 Go
Espace disponible : 7.5 Go

### commandes : 

#### hostname :
#### LANG :
La commande env affiche toutes les variables d’environnement dont LANG ($LANG) et LANG definissent la langue et la localisation du système. : LANG=en_US.UTF-8, il y a des enpact dasn les encodages.

root@server2:~# echo $LANG
en_US.UTF-8

#### hostname :
affiche le nom court de la machine, le nom que j'ai donné lors de l'installation. 

root@server2:~# hostname
server2


#### domaine :

pour le domaine, j'ai utiliser la commande man hotname, et j'ai trouveé la synopsis:  [-d|--domain]

root@server2:~# hostname -d
ufr-info-p6.jussieu.fr


### verification emplacement depots :

cat = concatenate
/etc/apt/sources.list : liste des depots 
grep -v -E '^#|^$' : affiche tout ce qui n'est pas un commentaire c'est le filtre. 

root@server2:~# cat /etc/apt/sources.list | grep -v -E '^#|^$'
deb http://deb.debian.org/debian/ bookworm main
deb http://security.debian.org/debian-security bookworm-security main
deb http://deb.debian.org/debian/ bookworm-updates main

sont les depots officiels que j'ai ajouté, dans la phase d'installation de Debian.
les adresses dans ce fichier sont utilisés par le système pour installer et mettre à jour les applications

Sources :
man grep

https://debian-facile.org/doc:systeme:apt:sources.list
https://www.it-connect.fr/les-fichiers-sources-list/



### passwd/shadow :

root@server2:~# cat /etc/shadow | grep -vE ':\*:|:!\*:'
root:$y$j9T$GAMurk4V5Yd3x0OFwR60O.$MfkHsnD8wZKIl6Cf9wkzCcSIxzSTnPudwZXLOLSRhE1:20464:0:99999:7:::
messagebus:!:20464::::::
avahi-autoipd:!:20464::::::
sshd:!:20464::::::
root@server2:~# 

/etc/shadow : contient les infos de mots de passe. 
ps: les mots de passe sont hachés ! 

la commande affiche les comptes avec un mot de passe actif. mais pour les comptes système bloqués ou désactivés, identifiés par les caractères * ou ! , ne apparaissent pas.

Sources :
man shadow
man grep
man regex

https://www.debian.org/doc/manuals/debian-reference/ch04.en.html
https://www.ionos.fr/digitalguide/serveur/configuration/linux-la-commande-grep/


### comptes utilisateurs : 

root@server2:~# cat /etc/passwd | grep -vE 'nologin|sync'
root:x:0:0:root:/root:/bin/bash
root@server2:~# 

/etc/passwd : contient les infos des utilisateurs. 
ça permet de voir les utilisateurs qui on un compte actif grace au filtre grep nologin|sync. 

### commandes fdisk -l et fdisk -x : 


root@server2:~# fdisk -l
Disk /dev/sda: 20 GiB, 21474836480 bytes, 41943040 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 550B93D1-FA80-456A-8051-AAFFBE298DB8

Device        Start      End  Sectors  Size Type
/dev/sda1      2048 19531775 19529728  9.3G Linux filesystem
/dev/sda2  19531776 27344895  7813120  3.7G Linux filesystem
/dev/sda3  27344896 29298687  1953792  954M Linux filesystem
/dev/sda4  29298688 41940991 12642304    6G Linux swap
root@server2:~# fdisk -x
Disk /dev/sda: 20 GiB, 21474836480 bytes, 41943040 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 550B93D1-FA80-456A-8051-AAFFBE298DB8
First usable LBA: 34
Last usable LBA: 41943006
Alternative LBA: 41943039
Partition entries starting LBA: 2
Allocated partition entries: 128
Partition entries ending LBA: 33

Device        Start      End  Sectors Type-UUID                            UUID                                 Name   Attrs
/dev/sda1      2048 19531775 19529728 0FC63DAF-8483-4772-8E79-3D69D8477DE4 1D641B8D-95BD-40CE-A343-77FCB0972FA8 la racine
                                                                                                                       
/dev/sda2  19531776 27344895  7813120 0FC63DAF-8483-4772-8E79-3D69D8477DE4 74B92FD0-3795-45D9-9347-37547EC231CE espace tempo
                                                                                                                       
/dev/sda3  27344896 29298687  1953792 0FC63DAF-8483-4772-8E79-3D69D8477DE4 EF3FB370-2FF2-403C-8483-2B6F088F1699 les logs
                                                                                                                       
/dev/sda4  29298688 41940991 12642304 0657FD6D-A4AB-43C4-84E5-0933C84B4F4F 0A4737FE-EEA2-4ED9-A85F-04DFE5DACC92 ma swap
                                                                           
root@server2:~# 


Les commandes fdisk -l et fdisk -x saffiche la structure du disque et la façon dont les partitions sont organisées.
La commande fdisk -l affiche les informations principales du disque, comme sa taille, son modèle, le type de table de partitions (GPT), demandee dans le tp et la liste des partitions, sans entrer dans des détails.
La commande fdisk -x montre les mêmes informations, mais de manière plus détaillée. Elle affiche par exemple les IDs et les noms des partitions.

ps: sont les partitions que j'ai créé.


source : 
man fdisk

-------------------------------------------------------------------------

Sources :

Documents mis à disposition par le professeur  
Commande Unix " man" 

https://www.lemagit.fr/conseil/26-commandes-Linux-pour-administrer-le-stockage#:~:text=L'option%20%2Dh%20affiche%20la,la%20taille%20totale%20du%20répertoire.&text=Affiche%20l'utilisation%20du%20système%20de%20fichiers%20sur%20votre%20machine%20Linux.
