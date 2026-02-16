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
for i in $@
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

OBJECT="$1"
USER="${USER}"

# check n parametres
if [ $# -ne 1 ]; then
  echo "Erreur : Il fault 1 paramentre pour ce shell"
  exit 1
fi

# check type
#dir
if [ -d "$OBJECT" ]; then
    echo $OBJECT" is a directory."
        echo "and is acessible to $USER en : "

if [ -x "$OBJECT" ]; then
    echo "exec"
fi
if [ -w "$OBJECT" ]; then
    echo "writin"
fi
if [ -r "$OBJECT" ]; then
    echo "read"
fi

    else
#file
if [ -f "$OBJECT" ]; then
        echo "$OBJECT is a file and : "
if [ -s "$OBJECT" ]; then
    echo "is not empty."
fi
        echo "is acessible to $USER en : "

if [ -x "$OBJECT" ]; then
    echo "exec"
fi
if [ -w "$OBJECT" ]; then
    echo "writin"
fi
if [ -r "$OBJECT" ]; then
    echo "read"
fi
else
    echo "$OBJECT does not exist."
fi
fi
```

source:
 https://linuxize.com/post/bash-check-if-file-exists/



## Exercice : Afficher le contenu d’un r´epertoire
## Exercice : Lister les utilisateurs
## Exercice : Mon utilisateur existe t’il
## Exercice : Creation utilisateur
## Exercice : lecture au clavier
## Exercice : appr´eciation
## 


