### TP 02 2 Processus

## Exercice : Etude des processus UNIX 

## 1 - 
command : 
root@server2:~# ps aux 
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.1  0.5 102144 12076 ?        Ss   14:34   0:00 /sbin/init
root           2  0.0  0.0      0     0 ?        S    14:34   0:00 [kthreadd]
...

# TIME: 
Dans la sortie de la commande ps aux (bah ps)  correspond au temps CPU cumulé utilisé par le processus depuis son démarrage. C'est le temps total pendant lequel le processeur a été activement utilisé par ce processus.

# Quel est le processus ayant le plus utilis´e le processeur sur votre machine ? *

root@server2:~# ps aux --sort=-%cpu
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         500  2.9 12.9 10392212 260764 ?     Ssl  14:34   0:24 /usr/bin/python3 /usr/bin/fail2ban-server -xf start
root         755  0.2  0.0      0     0 ?        I    14:40   0:01 [kworker/0:0-events]
root          47  0.1  0.0      0     0 ?        I    14:34   0:01 [kworker/0:2-cgwb_release]
root           1  0.0  0.6 102144 12108 ?        Ss   14:34   0:00 /sbin/init
...

La commande de base ps aux affiche tous les processus du vm et l'option --sort=-%cpu fait le tri des processus par utilisation du processeur, par le plus élevé.

donc c'est le processus fail2ban-server. 

# Quel a ´et´e le premier processus lanc´e apr`es le d´emarrage du syst`eme ? *

c'est le processus init, qui a le PID 1. donc systemd le noyau de linux.


# A quelle heure votre machine a-t-elle d´emarr´ee ? Trouvez une autre commande permetant de trouver le temps depuis lequel votre serveur tourne.

root@server2:~# who -b
system boot  Feb  5 14:34

il y a aussi la comande uptime quue affiche le up de la vm, le nombre d'utilisateurs connectés et la charge moyenne. 
15:01:03 up 26 min,  2 users,  load average: 0.00, 0.00, 0.00

je suis conecte dans la vm et aussi dans mon mac, donc j'ai 2 utilisateurs conectés.

# Pouvez-vous ´etablir le nombre approximatif de processus cr´e´es depuis le d´emarrage (“boot”) de votre machine ?

si chque processus a un PID unique croissante, alors le nombre de processus créés depuis le démarrage est 844.
j'ai utilisé la commande ps aux.  


## 2.2 -
# Trouver une option de la commande ps permettant d’afficher le PPID d’un processus.

root@server2:~# ps -o pid,ppid,cmd
    PID    PPID CMD
    771     765 -bash
    850     771 ps -o pid,ppid,cmd
root@server2:~# 

output format '-o' choisit quelles colonnes afficher, et après j'ai choisi d'afficher le PID, PPID et la commande, c'et mentione dansle man du ps.


# Donner la liste ordonn´ee de tous les processus ancˆetres de la commande ps en cours d’ex´ecution.

root@server2:~# ps -o pid,ppid,cmd
    PID    PPID CMD
    771     765 -bash
    895     771 ps -o pid,ppid,cmd
root@server2:~# ps -p 765 -o pid,ppid,cmd
    PID    PPID CMD
    765     516 sshd: root@pts/0
root@server2:~# ps -p 516 -o pid,ppid,cmd
    PID    PPID CMD
    516       1 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
root@server2:~# ps -p 1 -o pid,ppid,cmd
    PID    PPID CMD
      1       0 /sbin/init
root@server2:~# 

ou avec la commande ps axjf qui affiche les processus en arbre :
ps axjf

 1     507     507     507 tty1         761 Ss       0   0:00 /bin/login -p --
    507     761     761     507 tty1         761 S+       0   0:00  \_ -bash
      1     516     516     516 ?             -1 Ss       0   0:00 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
    516     765     765     765 ?             -1 Ss       0   0:00  \_ sshd: root@pts/0
    765     771     771     771 pts/0        902 Ss       0   0:00      \_ -bash
    771     902     902     771 pts/0        902 R+       0   0:00          \_ ps axjf

source: https://linux.goffinet.org/administration/processus-et-demarrage/processus-linux/

# 2.3
# Reprendre la question pr´ec´edente avec la commande pstree.
# Vous devrez sans doute installer ce package : voir apt update ; apt search ; apt install.

#On Ubuntu/Debian APT
root@server2:~# apt install psmisc

root@server2:~# pstree -p

source: https://gist.github.com/leafsummer/94beb97f62926b7e1fc399081f4ac45f

# 2.4
root@server2:~# top
top - 16:04:29 up  1:30,  3 users,  load average: 0.00, 0.01, 0.00
Tasks:  80 total,   1 running,  79 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.3 sy,  0.0 ni, 99.3 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st 
MiB Mem :   1967.3 total,   1460.5 free,    269.3 used,    382.2 buff/cache     
MiB Swap:   6173.0 total,   6173.0 free,      0.0 used.   1697.9 avail Mem 
...

---------------------------------------------

500 root      20   0    9.9g 260772 233444 S   1.7  12.9   1:46.32 fail2ban-server  

j'ai trié les processus par occupation mémoire avec la commande top (M), le plus gourmand est fail2ban-server.
Ce service permet de protéger le serveur contre les attaques par force brute en analysant les fichiers de logs, j'ai l'installé dans la première partie du TP2. 

---------------------------------------------
comando: Z pour metre de la couleur.
command : X  pour accentuer la colonne de tri. (c'est la %MEM)
command : T pour trier par temps CPU.

---------------------------------------------
root@server2:~# apt update
root@server2:~# apt install htop
root@server2:~# htop

La commande htop est plus facile à utiliser que top.
Elle affiche les processus avec des couleurs et une interface plus claire.
Cependant, htop n’est pas installé par défaut sur tous les systèmes, contrairement à top qui est toujours présent

source: https://fr.eitca.org/cybersecurity/eitc-is-lsa-linux-system-administration/linux-processes/state-niceness-and-processes-monitoring/examination-review-state-niceness-and-processes-monitoring/how-does-htop-differ-from-the-top-command-in-terms-of-functionality-and-user-interface/

---------------------------------------------

## 3 Exercice 2 : Arrˆet d’un processus

root@server2:~# nano date.sh
root@server2:~# nano date-toto.sh
root@server2:~# chmod +x date.sh date-toto.sh

date 17:10:33
toto 12:10:33   jobs
[1]-  Running                 ./date.sh &
[2]+  Running                 ./date-toto.sh &
root@server2:~# date 17:10:34

Les deux scripts utilisent une boucle infinie avec while true.
La sleep permet d’attendre une seconde entre chaque affichage.
La date est utilisée pour afficher l’heure, avec ou sans décalage horaire.(tot avec )

sources: 
https://www.reddit.com/r/linuxquestions/comments/ypkcj6/terminal_how_do_i_run_a_command_send_it_to_the/?tl=fr#:~:text=Si%20vous%20utilisez%20CTRL%2BZ,premier%20plan%20de%20votre%20terminal.

https://www.reddit.com/r/linuxquestions/comments/fbpc0u/how_to_make_a_bashshell_script_executable_by_name/?tl=fr#:~:text=Vous%20pouvez%20le%20rendre%20exécutable,d'écraser%20des%20binaires%20existants.

## 4 Exercice 3 : les tubes
# Quelle est la diff´erence entre tee et cat ?

La commande cat sert à afficher le contenu d’un fichier ou ce qui arrive par l’entrée standard.
La commande tee reçoit aussi l’entrée standard, mais elle l’affiche à l’écran et l’enregistre en même temps dans un fichier.
du coup, cat affiche seulement le texte, tandis que tee permet d’afficher le résultat tout en le sauvegardant dans un fichier.

ls | cat
reçoi la liste avec tube et reafiche un après l’autre, c’est comme un filtre.

ls -l | cat > liste
si liste n’existe pas, il le crée et y écrit la liste. Si liste existe déjà, il écrase son contenu avec la nouvelle liste, parce que le > redirige la sortie vers le fichier et ls -l affiche la liste détaillée des fichiers.

ls -l | tee liste
affiche la liste et en même temps l’enregistre dans le fichier liste. 

ls -l | tee liste | wc -l
prendre la liste des fichiers, l’enregistrer dans le fichier liste, puis compter le nombre de lignes, et affiche dans le terminal.

## 5 Journal syst`eme rsyslog

root@server2:~# systemctl status rsyslog
Unit rsyslog.service could not be found.
root@server2:~# apt update
...
root@server2:~# apt install rsyslog

Main PID: 13880 (rsyslogd)

- selon le ficher de config, les messages des services sont dans /var/log/syslog
les autres messages sont dans /
# Log commonly used facilities to their own log file
#
auth,authpriv.*			/var/log/auth.log
cron.*				-/var/log/cron.log
kern.*				-/var/log/kern.log
mail.*				-/var/log/mail.log
user.*				-/var/log/user.log

Le service cron sert à exécuter automatiquement des commandes ou des scripts à des moments précis ou de manière régulière.

https://blog.stephane-robert.info/docs/admin-serveurs/linux/cron/

tail -f permet d’afficher le contenu d’un fichier de log en temps réel, c’est-à-dire que les nouvelles lignes s’affichent dès qu’elles sont écrites.
dans /var/log/syslog apres la,ncer la commande systemctl restart cron, je peut voir les messages de redémarrage du service cron.

Le fichier /etc/logrotate.conf sert à gérer la rotation des fichiers de logs.
Il permet d’éviter que les fichiers de logs deviennent trop volumineux en les renommant, compressant ou supprimant automatiquement après un certain temps.

La commande dmesg affiche les messages du noyau Linux, notamment la détection du matériel au démarrage.
Sur ma machine, Linux détecte un processeur Intel(R) Core(TM) i7-9750H CPU @ 2.60GHz.
Il détecte aussi une carte réseau Intel(R) PRO/1000, avec le driver e1000.


------------------------------------------------------------------------------------------------------------------------------------

sources:

man ps
https://unix.stackexchange.com/questions/88613/how-is-it-possible-to-sort-ps-commands-cpu-field