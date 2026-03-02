# TP 03 : Shell bash


## Exercice : parametres

```
#!/bin/bash

echo "Bonjour, vous avez rentre $# de parametres parametres."
echo
echo "Le nom du script est $0"
echo
echo "Le 3eme parametre est: $3"
echo
echo "Voici la liste des parametres :"
for i in "$@"
do
    echo "Parametre: $i"
done
```
sortie: 
```
root@server2:~# nano analyse.sh
root@server2:~# ./analyse.sh param1 param2 param3
Bonjour, vous avez rentre 3 de parametres parametres.

Le nom du script est ./analyse.sh

Le 3eme parametre est: param3

Voici la liste des parametres :
Parametre: param1
Parametre: param2
Parametre: param3
root@server2:~# 
```
## Exercice : v´erification du nombre de param`etres
```
#!/bin/bash

if [ $# -ne 2 ]; then
  echo "Erreur : il faut exactement 2 paramtres."
  exit 1
fi

CONCAT="$1$2"
echo "$CONCAT"
```
sortie: 
```
root@server2:~# ./concat.sh Edu ardo
Eduardo
root@server2:~# ./concat.sh Edu ardo Ramos
Erreur : il faut exactement 2 paramètres.
root@server2:~# 
```

## Exercice : argument type et droits

```

#!/bin/bash

# Vérification du nombre de paramètres
if [ $# -ne 1 ]; then
  echo "Erreur : il faut exactement 1 paramètre."
  exit 1
fi

OBJECT="$1"
USER="$USER"

# Si n'existe pas
if [ ! -e "$OBJECT" ]; then
  echo "Le fichier $OBJECT n'existe pas."
  exit 1
fi

# Fonction permissions
getInfo() {
  echo "\"$OBJECT\" est accessible par $USER en :"
  if [ -r "$OBJECT" ]; then echo "lecture"; fi
  if [ -w "$OBJECT" ]; then echo "écriture"; fi
  if [ -x "$OBJECT" ]; then echo "exécution"; fi
}

# Type
if [ -d "$OBJECT" ]; then
  echo "Le fichier $OBJECT est un répertoire."
  getInfo
elif [ -f "$OBJECT" ]; then
  if [ -s "$OBJECT" ]; then
    echo "Le fichier $OBJECT est un fichier ordinaire qui n'est pas vide."
  else
    echo "Le fichier $OBJECT est un fichier ordinaire qui est vide."
  fi
  getInfo
else
  echo "Le fichier $OBJECT existe, mais ce n'est ni un fichier ordinaire ni un répertoire."
  getInfo
fi

```

sortie:

```
root@server2:/# root/test-fichier.sh '/etc'
Le fichier /etc est un répertoire.
"/etc" est accessible par root en :
lecture
écriture
exécution
root@server2:/# /root/test-fichier.sh /etc/passwd
Le fichier /etc/passwd est un fichier ordinaire qui n'est pas vide.
"/etc/passwd" est accessible par root en :
lecture
écriture
root@server2:/# 
```
source:
 https://linuxize.com/post/bash-check-if-file-exists/ <br>
 https://www.it-connect.fr/les-fonctions-en-bash-scripting-linux/ <br>s

 
## Exercice : Afficher le contenu d’un r´epertoire

root@server2:~# touch listedir.sh
root@server2:~# chmod +x listedir.sh
root@server2:~# nano listedir.sh 

```
#!/bin/bash

# check n paramtres
if [ $# -ne 1 ]; then
  echo "Erreur : il faut exactement 1 paramtre."
  exit 1
fi

DIR="$1"

# V  rifier que c'est un r  pertoire
if [ ! -d "$DIR" ]; then
  echo "Erreur : $DIR n'est pas un r  pertoire valide."
  exit 1
fi

echo "--------->fichiers dans $DIR/"

for f in "$DIR"/*; do
  if [ -f "$f" ]; then
    echo "$f"
  fi
done

echo "-------> repertoires dans $DIR/"

for d in "$DIR"/*; do
  if [ -d "$d" ]; then
    echo "$d"
  fi
done

```
sortie:

```
root@server2:/# root/listedir.sh /lib
--------->fichiers dans /lib/
/lib/klibc-XX6cASCB7KZyJWpIJW79y94XHBY.so
/lib/libsupp.a
/lib/os-release
/lib/sftp-server
-------> repertoires dans /lib/
/lib/apparmor
/lib/apt
/lib/binfmt.d
/lib/console-setup
/lib/dbus-1.0
/lib/discover
...

```

## Exercice : Lister les utilisateurs
root@server2:~# chmod +x listusers_cut.sh 
root@server2:~# ./listusers_cut.sh users

```                                              
#!/bin/bash

# check n paramtres
if [ $# -ne 1 ]; then
  echo "Erreur : il faut exactement 1 paramtre."
  exit 1
fi

for user in $(cat /etc/passwd); do
  echo $user
done

```

il parcourt les “mots” séparés par les espaces, et comme les champs de /etc/passwd sont séparés par des “:”, il affiche tout le contenu du fichier.

```
#!/bin/bash

if [ $# -ne 1 ]; then
  echo "Erreur : il faut exactement 1 paramtre."
  exit 1
fi

# On coupe pour avoir: login:uid
cut -d: -f1,3 /etc/passwd | while IFS=: read -r login uid; do
  if [ "$uid" -gt 100 ]; then
    echo "$login"
  fi
done


```
sortie:
les champs sont séparés par :
```
root@server2:~# ./listusers_cut2.sh users
nobody
systemd-network
systemd-timesync
avahi-autoipd
sshd
root@server2:~# 
```

la commande
awk.

```
#!/bin/bash


if [ $# -ne 1 ]; then
  echo "Erreur : il faut exactement 1 paramtre."
  exit 1
fi


awk -F: '$3 > 100 { print $1 }' /etc/passwd

```

sortie:
```
root@server2:~# chmod +x listusers_awk.sh
root@server2:~# ./listusers_awk.sh users
nobody
systemd-network
systemd-timesync
avahi-autoipd
sshd
root@server2:~# 
```

La commande for user in $(cat /etc/passwd) découpe le contenu par espaces et retours ligne, donc ce n’est pas fiable pour lire un fichier ligne par ligne.
“Avec cut, je récupère le login et le UID, puis je filtre UID > 100.”
“Avec awk, je filtre directement sur la 3e colonne (UID) et j’affiche la 1re colonne (login).”


## Exercice : Mon utilisateur existe t’il
## Exercice : Creation utilisateur
## Exercice : lecture au clavier
## Exercice : appr´eciation
## 


