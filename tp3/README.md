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





## Exercice : Afficher le contenu d’un r´epertoire
## Exercice : Lister les utilisateurs
## Exercice : Mon utilisateur existe t’il
## Exercice : Creation utilisateur
## Exercice : lecture au clavier
## Exercice : appr´eciation
## 


