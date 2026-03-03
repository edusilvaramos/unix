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

```
#!/bin/bash

# n param 
if [ "$#" -ne 1 ]; then
  exit 1
fi

arg="$1"

# arg est numbre
if [[ "$arg" =~ ^[0-9]+$ ]]; then
  # if le 3e champ (UID) == arg print UID
  awk -F: -v uid="$arg" '$3 == uid { print $3 }' /etc/passwd
else
  # cherche par login
  awk -F: -v login="$arg" '$1 == login { print $3 }' /etc/passwd
fi

```
sortie:

root@server2:~# nano user-exists.sh
root@server2:~# chmod +x user-exists.sh
root@server2:~# ./user-exists.sh root
0
root@server2:~# 

or 

root@server2:~# ./user-exists.sh user
root@server2:~# 
root@server2:~# ./user-exists.sh daemon
1
root@server2:~# ./user-exists.sh backup
34
root@server2:~# 

## Exercice : Creation utilisateur


```
#!/bin/bash

set -e

# user 
if [ "$(id -u)" -ne 0 ]; then
  echo "Erreur: ce script est exec by root."
  exit 1
fi

# if user exists
if [ ! -x "./user-exists.sh" ]; then
  echo "Erreur: ./user-exists.sh introuvable ou non excutable."
  echo "Astuce: chmod +x user-exists.sh"
  exit 1
fi

# make user
read -r -p "Login: " login
read -r -p "Nom: " nom
read -r -p "Prenom: " prenom
read -r -p "UID: " uid
read -r -p "GID: " gid
read -r -p "Commentaires: " comments

# rifier login/UID
uid_login=$(./user-exists.sh "$login" || true)
uid_uid=$(./user-exists.sh "$uid" || true)

# Si existe 
if [ -n "$uid_login" ]; then
  echo "$uid_login"
  exit 0
fi

if [ -n "$uid_uid" ]; then
  echo "$uid_uid"
  exit 0
fi

# if  /home/login n'existe   
if [ -d "/home/$login" ]; then
  echo "Erreur: /home/$login existe d  j  ."
  exit 1
fi

# create user
useradd -m -d "/home/$login" -u "$uid" -g "$gid" -c "$nom $prenom - $comments" "$login"  

# Mot de passe to user
passwd "$login"

echo "OK: $login"

```

sortie:

```

root@server2:~# ./create-user.sh 
Login: edu
Nom: edu
Prenom: edu
UID: 1234
GID: 12345
Commentaires: 
useradd: group '12345' does not exist
root@server2:~# 


## Exercice : lecture au clavier

Quitter more : q
Avancer d’une ligne : Enter
Avancer d’une page :  Space
Remonter d’une page : b
Chercher une chaîne : tape / mot puis Entrée
Occurrence suivante : n

```

```
#!/bin/bash

#  repertoire
if [ $# -ne 1 ]; then
  echo "Usage: $0 repertoire"
  exit 1
fi

rep="$1"

#  repertoire  ???
if [ ! -d "$rep" ]; then
  echo "Erreur: ce n'est pas un repertoire"
  exit 1
fi

# look tous les fichiers
for f in "$rep"/*; do

  # if pas un fichier
  if [ ! -f "$f" ]; then
    continue
  fi 

  # tester si c'est un fichier texte
  file "$f" | grep -qi "text"
  if [ $? -eq 0 ]; then

    echo -n "Voulez-vous visualiser le fichier $f ? (o/n) "
    read reponse

    if [ "$reponse" = "o" ] || [ "$reponse" = "O" ]; then
      more "$f"
    fi

  fi
done

```



## Exercice : appr´eciation


```
#!/bin/bash

while true
do
  echo -n "Entrez une note (0-20) (ou q pour quitter) : "
  read note

  # quitter
  if [ "$note" = "q" ]; then
    echo "Au revoir !"
    break
  fi

  # verifier si c'est un nombre
  if ! [[ "$note" =~ ^[0-9]+$ ]]; then
    echo "Erreur : veuillez entrer un nombre (ou q)."
    continue
  fi

  # verifier intervalle 0-20 (optionnel mais propre)
  if [ "$note" -lt 0 ] || [ "$note" -gt 20 ]; then
    echo "Erreur : la note doit être entre 0 et 20."
    continue
  fi

  # appreciation
  if [ "$note" -ge 16 ]; then
    echo "très bien"
  elif [ "$note" -ge 14 ]; then
    echo "bien"
  elif [ "$note" -ge 12 ]; then
    echo "assez bien"
  elif [ "$note" -ge 10 ]; then
    echo "moyen"
  else
    echo "insuffisant"
  fi
done

```

sortie:
```
root@server2:~# ./appreciation.sh
Entrez une note (ou q pour quitter) : 4
Entrez une note (ou q pour quitter) : q
Au revoir !
root@server2:~# nano appreciation.sh
root@server2:~# ./appreciation.sh
Entrez une note (0-20) (ou q pour quitter) : 5
insuffisant
Entrez une note (0-20) (ou q pour quitter) : 20
très bien
Entrez une note (0-20) (ou q pour quitter) : q
Au revoir !
root@server2:~# 
```
