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
  echo "Erreur : il faut exactement 2 param  tres."
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

source:
 https://linuxize.com/post/bash-check-if-file-exists/ <br>
 https://www.it-connect.fr/les-fonctions-en-bash-scripting-linux/ <br>s

 
## Exercice : Afficher le contenu d’un r´epertoire





## Exercice : Lister les utilisateurs
## Exercice : Mon utilisateur existe t’il
## Exercice : Creation utilisateur
## Exercice : lecture au clavier
## Exercice : appr´eciation
## 


