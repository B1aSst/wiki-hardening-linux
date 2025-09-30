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

### R62 : Désactiver les services non nécessaires

### R68 : Protéger les mots de passe stockés

### R80 : Réduire la surface d’attaque des services réseau

### Scan

## Recommandations I

### R2 : Configurer le BIOS/UEFI
