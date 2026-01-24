### TP 02 Services, processus signaux


### 1 Secure Shell : SSH 



### 1.1 Exercice : Connection ssh root (reprise fin tp-01)

ps: le debout de lexplication est dans le tp1. 

La connexion root via SSH était désactivée (PermitRootLogin no), ce qui est plus sécurisé.
Autoriser la connexion root avec un mot de passe (PermitRootLogin yes) est moins sécurisé, car un attaquant peut tenter de deviner le mot de passe root par force brute.
j'ai lu qu'il est recommandé soit de désactiver l’accès root en SSH, soit d’utiliser l’option PermitRootLogin prohibit-password, qui autorise root uniquement avec une clé SSH.

**sources :**

man man sshd_config 
https://forums.framboise314.fr/viewtopic.php?t=283
https://www.it-connect.fr/interdire-les-connexions-ssh-de-lutilisateur-root/

### 1.2 Exercice : Authentification par clef / G´en´eration de clefs

Pour generer les clefs : ssh-keygen -t rsa
ps: dans le tp il est marqué ssh-keygen -t dsa, mais j'ai utilise le chifrement rsacar DSA est un format ancien.
par rapport la phrase, j'ai laisse vide, mais il est recommande d'utiliser une phrase de passe pour securiser la clef privee, si elle est voleé.

Outils utilisés : Terminal macOS + OpenSSH (ssh-keygen).
le ssh-keygen est un outil qui genere deux fichiers dans le repertoire ~/.ssh/ :


* sources : *

man ssh-keygen

https://terrazone.io/types-of-ssh-keys/


### 1.3 Exercice : Authentification par clef / Connection serveur

de base ma clef publique est dans ~/.ssh/id_rsa.pub, et la clef privee dans ~/.ssh/id_rsa. Donc, sur mon server, j'ai fait les commandes : 

root@server2:/# cd /root/.ssh/

root@server2:~/.ssh# touch authorized_keys

root@server2:~/.ssh# ls -l

total 4
-rw-r--r-- 1 root root   0 Jan 24 02:00 authorized_keys
-rw-r--r-- 1 root root 142 Jan 11 02:04 known_hosts
root@server2:~/.ssh# 

aprés j'ai copié le contenu de ma clef publique (id_rsa.pub) dans le fichier authorized_keys sur le serveur : 

eduardoramos@MacBookPro-001 ~ % ssh-copy-id -i ~/.ssh/id_rsa.pub root@192.168.1.59
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/Users/eduardoramos/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@192.168.1.59's password: 

Number of key(s) added:        1

Now try logging into the machine, with: "ssh -i /Users/eduardoramos/.ssh/id_rsa 'root@192.168.1.59'"
and check to make sure that only the key(s) you wanted were added.

eduardoramos@MacBookPro-001 ~ % 

gestion des permissions :

root@server2:~/.ssh# ls -l

total 8
-rw-r--r-- 1 root root 585 Jan 24 02:09 authorized_keys
-rw-r--r-- 1 root root 142 Jan 11 02:04 known_hosts
root@server2:~/.ssh# chmod 700 /root/.ssh/authorized_keys

root@server2:~/.ssh# ls -l
total 8
-rwx------ 1 root root 585 Jan 24 02:09 authorized_keys
-rw-r--r-- 1 root root 142 Jan 11 02:04 known_hosts

root@server2:~/.ssh# 

chmod = change mode
7 → lecture + écriture + execution pour le propriétaire
0 → aucun droit pour le groupe
0 → aucun droit pour les autres utilisateurs

**sources :**

doc prof
https://www.it-connect.fr/les-droits-sous-linux/#google_vignette

### 1.4 Exercice : Authentification par clef : depuis la machine hote

.ssh/id_rsa est la clef prive, qui doit utiliser pour se connecter au serveur, parce que la clef publique est déjà copiée sur le serveur dans authorized_keys.

eduardoramos@MacBookPro-001 ~ % ssh -i ~/.ssh/id_rsa root@192.168.1.59

Linux server2 6.1.0-42-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.159-1 (2025-12-30) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sat Jan 24 02:45:54 2026 from 192.168.1.151
root@server2:~# 


### 1.5 Exercice : S´ecurisez

Sur le serveur, j’ai édité le fichier sshd_config:
root@server2:~# nano /etc/ssh/sshd_config


PermitRootLogin prohibit-password
- SSH uniquement par clé, et interdit toute authentification par mot de passe pour l'user root.

PasswordAuthentication no
PubkeyAuthentication yes
- Désactive l'authentification par mot de passe pour tous les utilisateurs.
- Autorise seulement l'authentification par clé.
et aprés j’ai redémarré le service SSH :
systemctl restart ssh

_attaqye de force brute:_

c’est quand un quelqu’un essaie de deviner le mot de passe en testant plusieurs combinaisons automatiquement, jusqu’à tomber sur la bonne, ici il utilise surtout un programme très rapide, qui teste toutes les combinaisons de mots de passe possibles, raison pour laquelle les mots de passe simples sont très vulnérables et que dans mon sshd_config c'est PasswordAuthentication no. 

_techniques de protection :_

Apres avoir lu les sources, voici quelques techniques pour sécuriser SSH que j'ai mis en place :


- Utiliser des mots de passe forts et complexes. (mais desactiver l'authentification par mot de passe)
- Limiter les tentatives de connexion.
    - pour limiter les tentatives de connexion, on peut utiliser des outils comme Fail2Ban, qui bloque les adresses IP après un certain nombre de tentatives échouées. il fait cela en surveillant les fichiers journaux (/var/log/auth.log). j'ai le installé sur mon serveur.
    - Commande pour installer Fail2Ban : apt-get install fail2ban
    - cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local et nano /etc/fail2ban/jail.local
    - systemctl restart fail2ban et systemctl enable fail2ban et systemctl status fail2ban pour verifier le status.
    - ps: j'ai utilises des variables internes de Fail2ban pour configurer le nombre de tentatives et le temps de banissement.
        - test:
        eduardoramos@MacBookPro-001 ~ % ssh test@192.168.1.59 
        testt@192.168.1.59: Permission denied (publickey).
        eduardoramos@MacBookPro-001 ~ % ssh test@192.168.1.59 
        test@192.168.1.59: Permission denied (publickey).
        eduardoramos@MacBookPro-001 ~ % ssh test@192.168.1.59
        test@192.168.1.59: Permission denied (publickey).
        eduardoramos@MacBookPro-001 ~ % ssh root@192.168.1.59

        root@server2:~# fail2ban-client status sshd
        Status for the jail: sshd
        |- Filter
        |  |- Currently failed:	1
        |  |- Total failed:	3
        |  `- Journal matches:	_SYSTEMD_UNIT=sshd.service + _COMM=sshd
        `- Actions
        |- Currently banned:	0
        |- Total banned:	0
        `- Banned IP list:
      
        root@server2:~# 

- Utiliser l'authentification par clé publique (comme fait).
- Désactiver l'accès SSH pour l'utilisateur root (comme fait ici avec PermitRootLogin prohibit-password).

- Mettre en place des pare-feux et des systèmes de détection d'intrusion. (ex: UFW / firewalld)

- Utiliser des protocoles de chiffrement de données et de connexion securisee.

**source :**
https://blog.stephane-robert.info/docs/securiser/durcissement/ssh/
https://blog.stephane-robert.info/docs/services/reseau/ssh/
https://linux-audit.com/ssh/audit-and-harden-your-ssh-configuration/
https://serverfault.com/questions/1001336/is-permitrootlogin-prohibit-password-still-necessary-when-passwordauthentication
https://man.openbsd.org/sshd_config#PasswordAuthentication
https://askubuntu.com/questions/449364/what-does-without-password-mean-in-sshd-config-file
https://www.ibm.com/fr-fr/think/topics/brute-force-attack
https://www.it-connect.fr/premiers-pas-avec-fail2ban/
