### TP 01 Installation Serveurs
Pour ce TP, j’ai installé VirtualBox version 7.2.4 sur mon ordinateur personnel. Mon PC est un Mac Intel (macOS), donc je n’ai pas eu de problèmes de compatibilité avec VirtualBox.

L’installation se fait avec une image NetBoot. J’ai téléchargé l’image mini.iso depuis le chemin suivant :
http://ftp.fr.debian.org/debian/dists/bookworm/main/installer-amd64/current/images/netboot/mini.iso

J’ai choisi un miroir Debian situé en France, afin de bénéficier d’une meilleure vitesse de téléchargement et d’une source fiable.
J’ai choisi la release bookworm (Debian 12) car il s’agit d’une version stable de Debian.
L’architecture amd64 correspond au type de processeur utilisé par ma machine virtuelle VirtualBox.

Pour le fichier mini.iso, le dernier update est du 05-Jan-2026 et la taille est 62 Mo

Donc, dans la phase d’installation de Debian :
pour le miroir Debian, je suis resté sur un miroir en France
pour les locales : fr_FR.UTF-8 et ajout de en_US.UTF-8
clavier : français
Mais je rencontre des problèmes de touches sur le clavier du MacBook : apparemment, l’installation utilise un clavier “standard” (comme sur Windows), donc certaines touches ne correspondent pas exactement.

ps : j'ai pas reussi a  maitre le Bootable flag ON dans partition racine dans "la racine".

----------------------------------------------------------------
