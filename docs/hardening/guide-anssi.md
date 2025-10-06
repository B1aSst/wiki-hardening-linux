# Recommandations de configuration d'un système GNU/LINUX - Guide ANSSI

Source : [Recommandations de sécurité relatives à un système GNU/Linux](https://cyber.gouv.fr/publications/recommandations-de-securite-relatives-un-systeme-gnulinux)

## Recommandations M

### R30 : Désactiver les comptes utilisateur inutilisés

Les comptes utilisateur inutilisés doivent être désactivés au niveau du système :

```bash
sudo usermod --lock --expiredate 1970-01-02 admin-toto
```

### R31 : Utiliser des mots de passe robustes

Le mot de passe des comptes utilisateur doit être choisi avec le plus grand soin et conformément aux recommandations actuelles :
* Utiliser une longeur suffisante : un minimum de 15 caractères
* Combiner plusieurs mots réels ou caractères aléatoires
* Inclure des caractères spéciaux dans la limite de ce qui est supporté par le système

Pour changer le mot de passe, on utilisera :

```bash
passwd admin-gmorice
```

### R53 : Éviter les fichiers ou répertoires sans utilisateur ou sans groupe connu

Les fichiers ou répertoires sans utilisateur ou groupe identifiable par le système doivent être analysés et éventuellement corrigés afin d’avoir un propriétaire ou un groupe connu du système.

La commande suivante permet de lister l’ensemble des fichiers qui n’ont plus d’utilisateur ou de groupe associé :

```bash
sudo find / -type f \( -nouser -o -nogroup \) -ls 2>/dev/null
```

Les répertoires accessibles à tous en écriture sont dans la majorité des cas utilisés comme zones de stockage temporaire, dans lequel une application enregistre des données pour les traiter.

De nombreuses applications les utilisent de façon incorrecte. Les fichiers temporaires peuvent alors être détournés de leur fonction première et être exploités comme rebond, par exemple pour une escalade de privilèges.

Un palliatif est alors d’activer le sticky bit sur le répertoire afin que seul l’utilisateur ou l’application qui a créé le fichier soit en mesure de le supprimer.

Cela évite ainsi qu’un utilisateur ou une application puisse arbitrairement supprimer ou remplacer
les fichiers temporaires d’un autre utilisateur ou d’une autre application.

### R54 : Activer le sticky bit sur les répertoires inscriptibles

Tous les répertoires accessibles en écriture par tous doivent avoir le sticky bit positionné.

La commande suivante permet de lister l’ensemble des répertoires modifiables par tous et sans sticky bit :

```bash
sudo find / -type d \( -perm -0002 -a \! -perm -1000 \) -ls 2>/dev/null
```

La commande suivante permet de lister l’ensemble des répertoires modifiables par tous et dont le propriétaire n’est pas root :

```bash
sudo find / -type d -perm -0002 -a \! -uid 0 -ls 2>/dev/null
```

### R56 : Éviter l’usage d’exécutables avec les droits spéciaux setuid et setgid

Seuls les logiciels de confiance issus, par exemple, de la distribution ou de dépôts officiels de paquets et spécifiquement conçus pour être utilisés avec les droits spéciaux setuid ou setgid peuvent avoir ces droits positionnés.

La commande suivante permet de lister l’ensemble des fichiers avec les droits spéciaux setuid et setgid présents sur le système :

```bash
sudo find / -type f -perm /6000 -ls 2>/dev/null
```

On obtient par exemple :

```
   797221    412 -rwxr-sr-x   1 root     _ssh       420224 Aug  1 17:02 /usr/bin/ssh-agent
   800800     24 -rwxr-sr-x   1 root     mail        23104 Dec 31  2024 /usr/bin/dotlockfile
   788014     52 -rwxr-sr-x   1 root     crontab     51936 Jun 13 10:30 /usr/bin/crontab
   782054     72 -rwsr-xr-x   1 root     root        70888 Apr 19 12:20 /usr/bin/chfn
   782060    116 -rwsr-xr-x   1 root     root       118168 Apr 19 12:20 /usr/bin/passwd
   781947     72 -rwsr-xr-x   1 root     root        72072 May 10 00:54 /usr/bin/mount
   781949     56 -rwsr-xr-x   1 root     root        55688 May 10 00:54 /usr/bin/umount
   781921     20 -rwsr-xr-x   1 root     root        18816 May 10 00:54 /usr/bin/newgrp
   790097    300 -rwsr-xr-x   1 root     root       306456 Jun 30 07:55 /usr/bin/sudo
   782058     32 -rwxr-sr-x   1 root     shadow      31256 Apr 19 12:20 /usr/bin/expiry
   782059     88 -rwsr-xr-x   1 root     root        88568 Apr 19 12:20 /usr/bin/gpasswd
   782052    112 -rwxr-sr-x   1 root     shadow     113848 Apr 19 12:20 /usr/bin/chage
   785855     84 -rwsr-xr-x   1 root     root        84360 May 10 00:54 /usr/bin/su
   782056     52 -rwsr-xr-x   1 root     root        52936 Apr 19 12:20 /usr/bin/chsh
   797227    484 -rwsr-xr-x   1 root     root       494144 Aug  1 17:02 /usr/lib/openssh/ssh-keysign
   797191     52 -rwsr-xr--   1 root     messagebus    51272 Mar  8  2025 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
   783795     44 -rwxr-sr-x   1 root     shadow        43256 Jun 29 19:40 /usr/sbin/unix_chkpwd
   819417   1500 -rwsr-xr-x   1 root     root        1533496 Mar 29  2025 /usr/sbin/exim4
```

La commande suivante permet de retirer les droits spéciaux setuid ou setgid :

```bash
sudo chmod u-s <fichier > # Retire le droit spécial setuid
sudo chmod g-s <fichier > # Retire le droit spécial setgid
```

Le cas le plus courant est l’exécutable dont le propriétaire est root avec les droits spéciaux setuid (setuid root) ou setgid (setgid root) qui s’exécute avec les privilèges de root et non ceux de l’utilisateur appelant. Ces exécutables permettent à un utilisateur non privilégié d’effectuer des actions pour lesquelles il n’a, a priori, pas de droit.

En présence de vulnérabilités, ces exécutables sont exploités en vue de fournir un shell root à un utilisateur malveillant, ou tout du moins de détourner l’usage légitime de l’exécutable.

Ces exécutables sont donc à étudier au cas par cas.

### R58 : N’installer que les paquets strictement nécessaires

Le choix des paquets doit conduire à une installation aussi minimale que possible, se limitant à ne sélectionner que ceux qui sont nécessaires au besoin.

Ces paquets sont généralement issus de serveurs appelés dépôts qui contiennent un ensemble de paquets accessibles et installables à partir d’un gestionnaire de parquets. Chaque distribution dispose de dépôts officiels configurés, par défaut, dans le gestionnaire de paquets lors de l’installation et qui sont gérés par les mainteneurs de la distribution. Ces dépôts fournissent principalement les paquets de « base », leur mise à jour et les mises à jour de sécurité.

Pour l'installation de la VM, nous avons bien choisi une installation de type "minimale" qui assure que seul les paquets primordials au système sont installés. Aucun paquet supplémentaire n'est présent à cet instant y compris l'interface graphique.

Il est toujours possible de lister l'ensemble des paquets installés sur le système avec :

```bash
sudo apt list --installed
```

### R59 : Utiliser des dépôts de paquets de confiance

Seuls les dépôts internes à l’organisation ou officiels (de la distribution ou de d’un éditeur) doivent être utilisés.

Pour le vérifier :

```bash
cat /etc/apt/sources.list
```

```
#deb cdrom:[Debian GNU/Linux 13.1.0 _Trixie_ - Official amd64 NETINST with firmware 20250906-10:22]/ trixie contrib main non-free-firmware

deb http://deb.debian.org/debian/ trixie main non-free-firmware
deb-src http://deb.debian.org/debian/ trixie main non-free-firmware

deb http://security.debian.org/debian-security trixie-security main non-free-firmware
deb-src http://security.debian.org/debian-security trixie-security main non-free-firmware

# trixie-updates, to get updates before a point release is made;
# see https://www.debian.org/doc/manuals/debian-reference/ch02.en.html#_updates_and_backports
deb http://deb.debian.org/debian/ trixie-updates main non-free-firmware
deb-src http://deb.debian.org/debian/ trixie-updates main non-free-firmware

[...]
```

Ici, nous utilisons les dépôts officiels Debian en déclinaison "stable", cela est indiqué par la présence de la distribution "trixie".
Dans chaque dépot, le système utilise les listes de distribution de paquets suivantes :
* "main" : l'ensemble des paquets disponibles sous Debian
* "non-free-firmware" : correspondant aux firmwares et drivers distribués par les constructeurs mais n'étant pas libres (il s'agit d'un cas réccurent dans la distribution de drivers d'où la séparation avec la liste "firmware")

### R61 : Effectuer des mises à jour régulières

Il est recommandé d’avoir une procédure de mise à jour de sécurité régulière et réactive.

```bash
sudo crontab -e
```

Ajouter l'entrée suivante :

```
# Update system every weeks 
0 1 * * 0 apt update && apt upgrade -y && apt autoremove --purge -y
```

### R62 : Désactiver les services non nécessaires

Seuls les composants strictement nécessaires au service rendu par le système doivent être installés. Tout service, et particulièrement ceux en écoute active sur le réseau, est un élément sensible. Seuls ceux connus et requis pour le fonctionnement et la maintenance doivent être installés. Il est recommandé de désinstaller les services dont la présence ne peut être justifiée ou de les désactiver si leur désinstallation n’est pas possible.

Pour les distributions sous systemd, la commande suivante permet de lister l’ensemble des services installés sur le système :

```bash
sudo systemctl list -units --type service
```

```
  UNIT                                     LOAD   ACTIVE SUB     DESCRIPTION                                   
  apparmor.service                         loaded active exited  Load AppArmor profiles
  console-setup.service                    loaded active exited  Set console font and keymap
  cron.service                             loaded active running Regular background program processing daemon
  dbus.service                             loaded active running D-Bus System Message Bus
  exim4.service                            loaded active running exim Mail Transport Agent
  getty@tty1.service                       loaded active running Getty on tty1
  ifup@enp0s3.service                      loaded active exited  ifup for enp0s3
  ifupdown-pre.service                     loaded active exited  Helper to synchronize boot up for ifupdown
  keyboard-setup.service                   loaded active exited  Set the console keyboard layout
  kmod-static-nodes.service                loaded active exited  Create List of Static Device Nodes
  networking.service                       loaded active exited  Raise network interfaces
  ssh.service                              loaded active running OpenBSD Secure Shell server
  systemd-binfmt.service                   loaded active exited  Set Up Additional Binary Formats
  systemd-journal-flush.service            loaded active exited  Flush Journal to Persistent Storage
  systemd-journald.service                 loaded active running Journal Service
  systemd-logind.service                   loaded active running User Login Management
  systemd-modules-load.service             loaded active exited  Load Kernel Modules
  systemd-random-seed.service              loaded active exited  Load/Save OS Random Seed
  systemd-remount-fs.service               loaded active exited  Remount Root and Kernel File Systems
  systemd-sysctl.service                   loaded active exited  Apply Kernel Variables
  systemd-timesyncd.service                loaded active running Network Time Synchronization
  systemd-tmpfiles-setup-dev-early.service loaded active exited  Create Static Device Nodes in /dev gracefully
  systemd-tmpfiles-setup-dev.service       loaded active exited  Create Static Device Nodes in /dev
  systemd-tmpfiles-setup.service           loaded active exited  Create System Files and Directories
  systemd-udev-load-credentials.service    loaded active exited  Load udev Rules from Credentials
  systemd-udev-trigger.service             loaded active exited  Coldplug All udev Devices
  systemd-udevd.service                    loaded active running Rule-based Manager for Device Events and Files
  systemd-user-sessions.service            loaded active exited  Permit User Sessions
  user-runtime-dir@1001.service            loaded active exited  User Runtime Directory /run/user/1001
  user@1001.service                        loaded active running User Manager for UID 1001
  wpa_supplicant.service                   loaded active running WPA supplicant
  wtmpdb-update-boot.service               loaded active exited  Write boot and shutdown times into wtmpdb

Legend: LOAD   → Reflects whether the unit definition was properly loaded.
        ACTIVE → The high-level unit activation state, i.e. generalization of SUB.
        SUB    → The low-level unit activation state, values depend on unit type.

32 loaded units listed. Pass --all to see loaded but inactive units, too.
To show all installed unit files use 'systemctl list-unit-files'.
```

Les services couramment installés sur les distributions sont :
* les services d’auto-configuration, tels DHCP 24 ou ZeroConf 25 , outre le fait qu’ils peuvent perturber le réseau sur lequel ils sont connectés, reçoivent des éléments dont la légitimité est difficile à assurer. Sauf besoin opérationnel, ceux-ci ne devraient pas s’exécuter sur un serveur ;
* les services de RPC (portmap, rpc.statd, rpcbind...) ne sont en pratique utilisés que pour un serveur NFS ;
* les services bureautiques comme dbus, hald, ConsoleKit, CUPS ou PolicyKit ne devraient pas s’exécuter sur un serveur ;
* le service avahi, utilisé pour la publication et la découverte automatique de services sur le réseau. Sauf besoin opérationnel, celui-ci ne devrait pas s’exécuter sur un serveur ;
* le serveur X, rarement utile sur un serveur.

On peut par la suite désactiver les services non nécessaires comme suit :

```bash
sudo systemctl stop exim4.service
sudo systemctl disable exim4.service
sudo systemctl stop wpa_supplicant.service
sudo systemctl disable wpa_supplicant.service
```

### R68 : Protéger les mots de passe stockés

Tout mot de passe doit être protégé par des mécanismes cryptographiques évitant de les exposer en clair à un attaquant. Pour cela, se référer à la section 4.6 Stockage de mots de passe du guide Recommandations relatives à l’authentification multifacteur et aux mots de passe.

PAM peut être configuré afin d’utiliser yescrypt en ajoutant dans le fichier `/etc/pam.d/common-password` la directive suivante :

```bash
sudo nano /etc/pam.d/common-password
```

```
password	required			pam_unix.so obscure yescrypt rounds=11
```

On s'assurera par la suite que les mots de passe des utilisateurs et des éventuels comptes de services concernés soient changés :

```
sudo passwd admin-gmorice
```

### R80 : Réduire la surface d’attaque des services réseau

Tous les services réseau doivent être en écoute sur les interfaces réseau adéquates.

La commande suivante permet de lister l’ensemble des services en écoute sur le réseau :

```bash
sudo apt install sockstat
sudo sockstat
```

On obtient :

```
USER     PROCESS              PID      PROTO  SOURCE ADDRESS            FOREIGN ADDRESS           STATE
root     dhcpcd               684      raw    *:17                      *:*                       CLOSED
root     dhcpcd               684      raw6   :::58                     :::*                      CLOSED
root     dhcpcd               684      raw6   :::17                     :::*                      CLOSED
root     dhcpcd               685      raw6   :::58                     :::*                      CLOSED
root     sshd                 774      tcp4   *:22                      *:*                       LISTEN
root     sshd                 774      tcp6   :::22                     :::*                      LISTEN
root     dhcpcd               1042     udp6   fe80::c3f:30f8:ab8a:b8cc:546 :::*                      CLOSED
root     dhcpcd               1058     udp6   fd17:625c:f037:2:871f:48a2:c774:d18:546 :::*                      CLOSED
root     dhcpcd               1109     udp4   10.0.2.15:68              *:*                       CLOSED
root     sshd-session         1117     tcp4   10.0.2.15:22              10.0.2.2:54538            ESTABLISHED
root     sshd-session         1140     tcp4   10.0.2.15:22              10.0.2.2:54538            ESTABLISHED
root     dhcpcd               1222     udp6   fd17:625c:f037:2:a00:27ff:fefd:b0fa:546 :::*                      CLOSED
```

On constate que seul le service SSH à une connexion en écoute sur le port 22.

### Scan

Après application des recommandations de niveau M du guide, on éffectue un nouveau scan Lynis :

```bash
sudo lynis audit system
```

Voici les différences constatées :

* Le nombre de services en cours d'exécution ont réduit :

```
  - Check running services (systemctl)                        [ DONE ]
        Result: found 9 running services
  - Check enabled services at boot (systemctl)                [ DONE ]
        Result: found 15 enabled services
```

* Le nombre d'exécutable impactés par au moins un bit d'exécution a diminué :

```
  - Total without nodev:4 noexec:7 nosuid:2 ro or noexec (W^X): 7 of total 24
```

* Plus aucun service de gestion d'e-mail n'est en cours d'exécution :

```
[+] Software: e-mail and messaging
------------------------------------

```

* Par effet de bord, une réduction du nombre de processus non protégés par AppArmor :

```
    - Checking AppArmor status                                [ ENABLED ]
        Found 33 unconfined processes
```

## Recommandations I

### R2 : Configurer le BIOS/UEFI
### R3 : Activer le démarrage sécurisé UEFI
### R5 : Configurer un mot de passe pour le chargeur de démarrage
### R8 : Paramétrer les options de configuration de la mémoire
### R9 : Paramétrer les options de configuration du noyau
### R11 : Activer et configurer le LSM Yama
### R12 : Paramétrer les options de configuration du réseau IPv4
### R13 : Désactiver le plan IPv6
### R28 : Partitionnement type
### R32 : Expirer les sessions utilisateur locales
### R33 : Assurer l'imputabilité des actions d'administration
### R34 : Désactiver les comptes de service
### R35 : Utiliser des comptes de service uniques et exclusifs
### R39 : Modifier les directives de configuration sudo
### R40 : tiliser des utilisateurs cibles non-privilégiés pour les commandes sudo
### R42 : Bannir les négations dans les spécifications sudo
### R43 : Préciser les arguments dans les spécifications sudo
### R44 : Editer les fichiers de manière sécurisée avec sudo
### R50 : Restreindre les droits d'accès aux fichiers et aux répertoires sensibles
### R55 : Séparer les répertoires temporaires des utilisateurs
### R63 : Désactiver les fonctionnalités des services non essentielles
### R67 : Sécuriser les authentifications distante par PAM
### R69 : Sécuriser les accès aux bases utilisateur distantes
### R70 : Séparer les comptes système et d'administrateur de l'annuaire
### R74 : Durcir le service de messagerie locale
### R75 : Configurer un alias de messagerie des comptes de service
### R79 : Durcir et surveiller les services exposés

## Recommandations R
### R1 :
### R7 :
### R10 :
### R29 :
### R36 :
### R37 :
### R38 :
### R41 :
### R45 :
### R51 :
### R57 :
### R60 :
### R64 :
### R65 :
### R71 :
### R72 :
### R73 :
### R78 :

## Recommandations E
### R4 :
### R6 :
### R15 :
### R16 :
### R17 :
### R18 :
### R19 :
### R20 :
### R21 :
### R22 :
### R23 :
### R24 :
### R25 :
### R26 :
### R27 :
### R46 :
### R47 :
### R48 :
### R49 :
### R66 :
### R76 :
### R77 :