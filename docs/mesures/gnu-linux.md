# Système GNU/LINUX

*Recommandations de configuration d'un système GNU/LINUX - Guide ANSSI*

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

### R2 : Configurer le BIOS/UEFI

Il est conseillé d’appliquer les recommandations de la configuration du BIOS/UEFI
mentionnées dans la note technique « Recommandations de configuration matérielle
de postes clients et serveurs x86 ».

### R3 : Activer le démarrage sécurisé UEFI
Il est recommandé d’activer la configuration du démarrage sécurisé UEFI associée à
la distribution.

### R5 : Configurer un mot de passe pour le chargeur de démarrage
Un chargeur de démarrage permettant de protéger son démarrage par mot de passe
est à privilégier. Ce mot de passe doit empêcher un utilisateur quelconque de modi-
fier ses options de configuration.

Pour GRUB :

```bash
sudo grub-mkpasswd-pbkdf2
```

Entrer le mot de passe et copier le hash généré :

```bash
PBKDF2 hash of your password is <hash here>
```

Ajouter les lignes suivantes dans le fichier `/etc/grub.d/40_custom` :

```
set superusers="gleguellec"
password_pbkdf2 gleguellec <paste the hash here>
```

Puis éditer le fichier `/etc/default/grub` :

```bash
sudo nano /etc/default/grub
```

Ajouter ou modifier la ligne suivante :

```bash
GRUB_DISABLE_RECOVERY="true"
```

Enfin, mettre à jour la configuration de GRUB :

```bash
sudo update-grub
```

### R8 : Paramétrer les options de configuration de la mémoire
Les options de configuration de la mémoire recommandées sont les suivantes.

Les options de configuration de la mémoire détaillées dans cette liste sont à ajouter
à la liste des paramètres du noyau lors du démarrage en plus de celles déjà présentes
dans le fichier de configuration du chargeur de démarrage :

`l1tf=full,force` : active sans possibilité de désactivation ultérieure toutes les contre-mesures pour la vulnérabilité L1 Terminal Fault (L1TF) présente sur la plu-
part des processeurs Intel (en 2018 tout du moins). À noter que cela désactive
Symmetric MultiThreading (SMT) et peut donc avoir un impact fort sur les per-
formances du système. Cette option n’est toutefois nécessaire que lorsque le système est susceptible d’être utilisé comme hyperviseur. Si les machines virtuelles sont de confiance, c’est-à-dire avec un système d’exploitation invité à la fois de confiance et protégé contre la vulnérabilité L1TF, cette option n’est pas nécessaire et peut même être remplacée par `l1tf=off` pour maximiser les performances ;

`page_poison=on` : active le poisoning des pages de l’allocateur de pages (buddy
allocator). Cette fonctionnalité permet de remplir les pages libérées avec des
motifs lors de leur libération et de vérifier les motifs lors de leur allocation. Ce
remplissage permet de réduire le risque de fuites d’informations à partir des don-
nées libérées ;

`pti=on` : force l’utilisation de Page Table Isolation (PTI) y compris sur les pro-
cesseurs se prétendant non impactés par la vulnérabilité Meltdown ;

`slab_nomerge=yes` (équivalent à CONFIG_SLAB_MERGE_DEFAULT=n) : désactive la
fusion de caches slabs (allocations mémoire dynamiques) de taille identique.
Cette fonctionnalité permet de différentier les allocations entre les différents caches slabs, et complique fortement les méthodologies de pétrissage du tas (heap
massaging) en cas de heap overflow ;

`slub_debug=FZP` : active certaines options de vérification des caches slabs (alloca-
tions mémoire dynamiques) :
> F active les tests de cohérence des métadonnées des caches slabs,
> Z active le Red Zoning ; dans un cache slab, ajoute une zone rouge après chaque
objet afin de détecter des écritures après celui-ci. Il est important de noter que
la valeur utilisée pour la zone rouge n’est pas aléatoire et qu’il s’agit donc d’un
durcissement bien plus faible que l’utilisation de véritables canaris,
> P active le poisoning des objets et du padding, c’est-à-dire provoque une erreur
lors de l’accès aux zones empoisonnées ;

`spec_store_bypass_disable=seccomp` : force le système à utiliser la contre-mesure
par défaut (sur un système x86 supportant seccomp) pour la vulnérabilité Spectre
v4 (Speculative Store Bypass) ;

`spectre_v2=on` : force le système à utiliser une contre-mesure pour la vulnérabilité
Spectre v2 (Branch Target Injection). Cette option active spectre_v2_user=on
qui évite les attaques Single Threaded Indirect Branch Predictors (STIBP)
et Indirect Branch Prediction Barrier 12(IBPB) ;

`mds=full,nosmt` : force le système à utiliser Microarchitectural Data Sampling
(MDS) pour atténuer les vulnérabilités des processeurs Intel. L’option mds=full,
qui laisse Symmetric MultiThreading (SMT) activé, n’est donc pas une atténua-
tion complète. Cette atténuation nécessite une mise à jour du microcode Intel
et atténue également la vulnérabilité TSX Asynchronous Abort (TAA) du proces-
seur Intel sur les systèmes affectés par MDS ;

`mce=0` : force un kernel panic sur les erreurs non corrigées signalées par le sup-
port Machine Check. Sinon, certaines d’entre elles provoquent uniquement l’en-
voi d’un SIGBUS, permettant potentiellement à un processus malveillant de conti-
nuer à essayer d’exploiter une vulnérabilité par exemple Rowhammer ;

`page_alloc.shuffle=1` (équivalent à CONFIG_SHUFFLE_PAGE_ALLOCATOR=y) : ac-
tive le Page allocator randomization qui améliore les performances pour l’uti-
lisation du direct-mapped memory-side-cache mais réduit la prévisibilité des al-
locations de page et complète ainsi SLAB_FREELIST_RANDOM ;

`rng_core.default_quality=500` : augmente la confiance dans HWRNG du TPM
pour une initialisation robuste et rapide du CSPRNG de Linux en créditant la moitié
de l’entropie qu’il fournit.

Editer le fichier `/etc/default/grub` :
```bash
sudo nano /etc/default/grub
```

Ajouter ou modifier la ligne suivante :
```bash
# On ajoute les options de configuration de la mémoire
GRUB_CMDLINE_LINUX_DEFAULT="quiet l1tf=full,force npage_poison=on pti=on slab_nomerge=yes slub_debug=FZP spec_store_bypass_disable=seccomp spectre_v2=on mds=full,nosmt mce=0 page_alloc.shuffle=1 rng_core.default_quality=500"
```

### R9 : Paramétrer les options de configuration du noyau

La liste ci-dessous présente les options de configuration du noyau recommandées.

Editer le fichier `/etc/sysctl.conf` :

```bash
# Restreint l'accès au buffer dmesg (équivalent à CONFIG_SECURITY_DMESG_RESTRICT=y)
kernel.dmesg_restrict=1
# Cache les adresses noyau dans /proc et les différentes autres interfaces, 
# y compris aux utilisateurs privilégiés
kernel.kptr_restrict=2
# Spécifie explicitement l'espace d'identifiants de processus supporté par le noyau, 
# 65 536 étant une valeur donnée à titre d'exemple
kernel.pid_max=65536
# Restreint l'utilisation du sous -système perf
kernel.perf_cpu_time_max_percent=1
kernel.perf_event_max_sample_rate=1
# Interdit l'accès non privilégié à l'appel système perf_event_open (). 
# Avec une valeur plus grande que 2, on impose la possession de CAP_SYS_ADMIN, 
# pour pouvoir recueillir les évènements perf.
kernel.perf_event_paranoid=2
# Active l'ASLR
kernel.randomize_va_space=2
# Désactive les combinaisons de touches magiques (Magic System Request Key)
kernel.sysrq=0
# Restreint l'usage du BPF noyau aux utilisateurs privilégiés
kernel.unprivileged_bpf_disabled=1
# Arrête complètement le système en cas de comportement inattendu du noyau Linux
kernel.panic_on_oops=1
```

Pour appliquer les modifications :
```bash
sudo sysctl -p
```

### R11 : Activer et configurer le LSM Yama
Il est recommandé de charger le module de sécurité Yama lors du démarrage, par
exemple en passant la directive security=yama au noyau, et d’affecter à l’option de
configuration du noyau kernel.yama.ptrace_scope une valeur au moins égale à 1.

Editer le fichier `/etc/default/grub` :

Ajouter ou modifier la ligne suivante :

```bash
# On ajoute security=yama
GRUB_CMDLINE_LINUX_DEFAULT="quiet security=yama"
```

```bash
sudo update-grub
```

Editer le fichier `/etc/sysctl.conf` :
```bash
kernel.yama.ptrace_scope=1
```

Pour appliquer les modifications :
```bash
sudo sysctl -p
```

### R12 : Paramétrer les options de configuration du réseau IPv4
La liste ci-dessous présente les options de configuration du réseau IPv4 pour un hôte
« serveur » typique qui n’effectue pas de routage et ayant une configuration IPv4
minimaliste (adressage statique).

Editer le fichier `/etc/sysctl.conf` :

```bash
# Atténuation de l'effet de dispersion du JIT noyau au coût d'un compromis sur
# les performances associées.
net.core.bpf_jit_harden=2
# Pas de routage entre les interfaces. Cette option est spéciale et peut
# entrainer des modifications d'autres options. En plaçant cette option au plus
# tôt , on s'assure que la configuration des options suivantes ne change pas.
net.ipv4.ip_forward=0
# Considère comme invalides les paquets reçus de l'extérieur ayant comme source
# le réseau 127/8.
net.ipv4.conf.all.accept_local=0
# Refuse la réception de paquet ICMP redirect. Le paramétrage suggéré de cette
# option est à considérer fortement dans le cas de routeurs qui ne doivent pas
# dépendre d'un élément extérieur pour déterminer le calcul d'une route. Même
# pour le cas de machines non -routeurs , ce paramétrage prémunit contre les
# détournements de trafic avec des paquets de type ICMP redirect.
net.ipv4.conf.all.accept_redirects=0
net.ipv4.conf.default.accept_redirects=0
net.ipv4.conf.all.secure_redirects=0
net.ipv4.conf.default.secure_redirects=0
net.ipv4.conf.all.shared_media=0
net.ipv4.conf.default.shared_media=0
# Refuse les informations d'en -têtes de source routing fournies par le paquet
# pour déterminer sa route.
net.ipv4.conf.all.accept_source_route=0
net.ipv4.conf.default.accept_source_route=0
# Empêche le noyau Linux de gérer la table ARP globalement. Par défaut , il peut
# répondre à une requête ARP d'une interface X avec les informations d'une
# interface Y. Ce comportement est problématique pour les routeurs et les
# équipements d'un système en haute disponibilité (VRRP ...).
net.ipv4.conf.all.arp_filter=1
# Ne répond aux sollicitations ARP que si l'adresse source et destination sont sur
# le même réseau et sur l'interface sur laquelle le paquet a été reçu. Il est à
# noter que la configuration de cette option est à étudier selon le cas d'usage.
net.ipv4.conf.all.arp_ignore=2
# Refuse le routage de paquet dont l'adresse source ou destination est celle de la
# boucle locale. Cela interdit l'émission de paquet ayant comme source 127/8.
net.ipv4.conf.all.route_localnet=0
# Ignore les sollicitations de type gratuitous ARP. Cette configuration est
# efficace contre les attaques de type ARP poisoning mais ne s'applique qu 'en
# association avec un ou plusieurs proxy ARP maîtrisés. Elle peut également être
# problématique sur un réseau avec des équipements en haute disponibilité (VRRP ...)
net.ipv4.conf.all.drop_gratuitous_arp=1
# Vérifie que l'adresse source des paquets reçus sur une interface donnée aurait
# bien été contactée via cette même interface. À défaut , le paquet est ignoré.
# Selon l'usage , la valeur 1 peut permettre d'accroître la vérification à
# l'ensemble des interfaces , lorsque l'équipement est un routeur dont le calcul de
# routes est dynamique. Le lecteur intéressé est renvoyé à la RFC3704 pour tout
# complément concernant cette fonctionnalité.
net.ipv4.conf.default.rp_filter=1
net.ipv4.conf.all.rp_filter=1
# Cette option ne doit être mise à 1 que dans le cas d'un routeur , car pour ces
# équipements l'envoi de ICMP redirect est un comportement normal. Un équipement
# terminal n'a pas de raison de recevoir un flux dont il n'est pas destinataire et
# donc d'émettre un paquet ICMP redirect.
net.ipv4.conf.default.send_redirects=0
net.ipv4.conf.all.send_redirects=0
# Ignorer les réponses non conformes à la RFC 1122
net.ipv4.icmp_ignore_bogus_error_responses =1
# Augmenter la plage pour les ports éphémères
net.ipv4.ip_local_port_range=32768 65535
# RFC 1337
net.ipv4.tcp_rfc1337=1
# Utilise les SYN cookies. Cette option permet la prévention d'attaque de
# type SYN flood.
net.ipv4.tcp_syncookies=1
```

Pour appliquer les modifications :
```bash
sudo sysctl -p
```

### R13 : Désactiver le plan IPv6
Quand IPv6 n’est pas utilisé, il est recommandé de désactiver la pile IPv6.

Editer le fichier `/etc/default/grub` :
```bash
sudo nano /etc/default/grub
```

Ajouter ou modifier la ligne suivante :

```bash
# On ajoute ipv6.disable=1
GRUB_CMDLINE_LINUX_DEFAULT="ipv6.disable=1"
```

```bash
sudo update-grub
```

Editer le fichier `/etc/sysctl.conf` :
```bash
net.ipv6.conf.default.disable_ipv6=1
net.ipv6.conf.all.disable_ipv6=1
```

Pour appliquer les modifications :
```bash
sudo sysctl -p
```

### R28 : Partitionnement type

| Point de montage | Options | Description |
|-------------------|----------|--------------|
| `/` | *<sans option>* | Partition racine, contient le reste de l’arborescence |
| `/boot` | `nosuid,nodev,noexec` <br> *(noauto optionnel)* | Contient le noyau et le chargeur de démarrage. Pas d’accès nécessaire une fois le boot terminé (sauf mise à jour) |
| `/opt` | `nosuid,nodev` <br> *(ro optionnel)* | Packages additionnels au système. Montage en lecture seule si non utilisé |
| `/tmp` | `nosuid,nodev,noexec` | Fichiers temporaires. Ne doit contenir que des éléments non exécutables. Nettoyé après redémarrage ou de type *tmpfs* de préférence |
| `/srv` | `nosuid,nodev` <br> *(noexec,ro optionnels)* | Contient des fichiers servis par un service type Web, FTP… |
| `/home` | `nosuid,nodev,noexec` | Contient les répertoires HOME des utilisateurs |
| `/proc` | `hidepid=2` | Contient des informations sur les processus et le système |
| `/usr` | `nodev` | Contient la majorité des utilitaires et fichiers système |
| `/var` | `nosuid,nodev,noexec` | Partition contenant des fichiers variables pendant la vie du système (mails, fichiers PID, bases de données d’un service) |
| `/var/log` | `nosuid,nodev,noexec` | Contient les logs du système |
| `/var/tmp` | `nosuid,nodev,noexec` | Fichiers temporaires conservés après extinction |


Idéalement, on configurerait les paramètres de montage ci-dessus dans le fichier `/etc/fstab`.

Ici, nous n'avons qu'une seule partition `/` et une partition `/boot` dédiée. Il faudrait créer les autres partitions et configurer le montage avec les options recommandées dès l'installation du système.

Dans `/etc/fstab`, on configure le montage de `/boot` comme suit :

```bash
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
UUID=xxxx-xxxx  /boot           ext4    defaults,nosuid,nodev,noexec,noauto 0       2
```

### R32 : Expirer les sessions utilisateur locales
Les sessions utilisateur locales doivent expirer après une période d’inactivité.

Ajouter ceci dans `/etc/profile` ou un fichier sous `/etc/profile.d/` :

```bash
# Expire les sessions après 5 minutes d'inactivité
export TMOUT=300
```

Le serveur étant sans interface graphique, aucune configuration additionnelle n'est nécessaire pour les sessions graphiques.

### R33 : Assurer l'imputabilité des actions d'administration

Les actions d’administration nécessitant des privilèges doivent être tracées afin de
pouvoir identifier l’administrateur à l’origine d’une activité malveillante.

Plusieurs approches permettent de répondre à cette problématique :

- Utiliser des comptes d'administation dédiés et de sudo

Comptes nominatifs d’administration dédiés (local ou distant).
Secrets d’authentification différents suivant le compte utilisé.
Désactivation du compte root.
Utilisation de sudo.

La désactivation d’un compte peut passer par l’invalidation de ce compte au niveau
de son mot de passe (suppression du champ pw_passwd dans le shadow et shell de
login à /bin/false).

```bash
# Verrouillage d'un compte
usermod -L -e 1 <compte >
# Désactivation du shell de login
usermod -s /bin/false <compte >
```

- Journalisation de la création de tout process

Il est possible de journaliser la création de processus sur le système avec les règles
auditd suivantes :

```bash
sudo apt install auditd
```

Dans le fichier `/etc/audit/rules.d/audit.rules` :

```bash
-a exit ,always -F arch=b64 -S execve ,execveat
-a exit ,always -F arch=b32 -S execve ,execveat
```

Il est important de réaliser que le volume de log généré par cette méthode peut
être très important en fonction des tâches effectuées sur le système. Il est donc
conseillé d’utiliser cette méthode en combinaison d’un export de logs régulier
comme conseillé dans le guide Recommandations de sécurité pour l’architecture d’un
système de journalisation.

### R34 : Désactiver les comptes de service

La connexion à des comptes de service doivent être désactivée pour réduire la surface d’attaque.

```bash
sudo usermod -s /bin/false <service_account>

# Pour vérifier :
getent passwd <service_account>
# www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
```

### R35 : Utiliser des comptes de service uniques et exclusifs

Chaque service doit utiliser un compte de service unique et exclusif.

```bash
# Crée un compte de service dédié
sudo useradd -r -s /bin/false <service_account>
```

### R39 : Modifier les directives de configuration sudo

Les directives suivantes doivent être activées par défaut :
`noexec` appliquer le tag NOEXEC par défaut sur les commandes
`requiretty` imposer à l’utilisateur d’avoir un tty de login
`use_pty` utiliser un pseudo-tty lorsqu’une commande est exécutée
`umask=0077` forcer umask à un masque plus restrictif
`ignore_dot` ignorer le « . » dans $PATH
`env_reset` réinitialiser les variables d’environnement

Editer le fichier `/etc/sudoers` :

```bash
sudo nano /etc/sudoers
```

Ajouter ou modifier les lignes suivantes :

```bash
Defaults noexec ,requiretty ,use_pty ,umask =0027
Defaults ignore_dot ,env_reset
```

Il est ensuite possible de surcharger la valeur par défaut si nécessaire lors de la définition d’un droit (1) ou pour tout un groupe (2).

```bash
myuser ALL = EXEC: /usr/bin/mycommand # (1)
Defaults :% admins !noexec # (2
```

### R40 : Utiliser des utilisateurs cibles non-privilégiés pour les commandes sudo
Les utilisateurs cibles d’une règle doivent autant que possible être des utilisateurs
non privilégiés (c’est-à-dire non root).

### R42 : Bannir les négations dans les spécifications sudo
Les spécifications de commande ne doivent pas faire intervenir de négation.

### R43 : Préciser les arguments dans les spécifications sudo
Toutes les commandes du fichier de configuration sudoers doivent préciser stricte-
ment les arguments autorisés à être utilisés pour un utilisateur donné.

Eviter l'usage de wildcards (*) dans les spécifications de commande.
Spécifier l'absence d'argument par "".

### R44 : Editer les fichiers de manière sécurisée avec sudo

L'édition d’un fichier par sudo doit être réalisée au travers de l’éditeur de texte
sudoedit.

```bash
sudoedit <file>
```

### R50 : Restreindre les droits d'accès aux fichiers et aux répertoires sensibles
Les fichiers et les répertoires sensibles ne doivent être lisibles que par les utilisateurs ayant le strict besoin d’en connaître.

Tous les fichiers sensibles, générés ou installés lors l’installation du système, et ceux qui concourent aux mécanismes d’authentification (certificats d’autorité racine, clés publiques, certificats…) doivent être mis en place dès l’installation du système.

1. les fichiers ou répertoires sensibles système doivent avoir comme propriétaire
root afin d’éviter tout changement de droit par un utilisateur non privilégié ;
2. les fichiers ou répertoires sensibles accessibles à un utilisateur différent de root
(par exemple, la base des mots de passe d’un serveur Web) doivent avoir comme
propriétaire cet utilisateur (par exemple, l’utilisateur associé au serveur Web) qui
doit être membre d’un groupe dédié (par exemple, le groupe www-group) et qui
aura un droit d’accès en lecture seule à ce fichier ou répertoire ;
3. le reste des utilisateurs ne doit posséder aucun droit sur les fichiers ou répertoires
sensibles.

```bash
# Exemple de configuration de droits pour un fichier sensible
sudo chown root:root /etc/sensitive_file
sudo chmod 640 /etc/sensitive_file
```

### R55 : Séparer les répertoires temporaires des utilisateurs

Chaque utilisateur ou application doit posséder son propre répertoire temporaire et
en disposer exclusivement.

`pam_mktemp` et `pam_namespace` sont deux modules PAM qui permettent de créer des répertoires temporaires exclusifs à chaque utilisateur. (voir R67)

### R63 : Désactiver les fonctionnalités des services non essentielles

Les fonctionnalités configurées au niveau des services démarrés doivent être limitées
au strict nécessaire.

```bash
# Liste l'ensemble des fichiers exécutables pour lesquels un ou plusieurs Linux capabilities ont été activées
find / -type f -perm /111 -exec getcap {} \; 2>/dev/null
```

Les exécutables listés doivent être revus afin de s’assurer que les Linux capabilities activées sont nécessaires à leur bon fonctionnement. En effet, le noyau Linux offre 41 capabilities à ce jour. Au minimum 19 d’entre elles per-
mettent d’élever trivialement ses droits à celui de l’utilisateur root (UID=0) (full
root). La plupart des autres peuvent également fortement aider à obtenir un ac-
cès root sur le système.

### R67 : Sécuriser les authentifications distante par PAM

Quand l’authentification se déroule au travers d’une application distante (réseau), le
protocole d’authentification utilisé par PAM doit être sécurisé (chiffrement du flux,
authentification du serveur distant, mécanismes d’anti-rejeu…).

`pam_faillock` permet de bloquer temporairement un compte après un certain nombre d’échecs ;

`pam_time` permet de restreindre les accès à une plage horaire ;

`pam_passwdqc` permet d’appliquer des contraintes suivant une politique de complexité de mots de passe ;

`pam_pwquality` permet de tester la robustesse des mots de passe ;

`pam_wheel` permet de restreindre un accès aux utilisateurs membres d’un groupe particulier (wheel par défaut) ;

`pam_mktemp` permet de fournir des répertoires temporaires privés par utilisateur sous /tmp ;

`pam_namespace` permet de créer un espace de noms privés par utilisateur.

Dans le fichier `/etc/pam.d/su` :

```bash
# Limite l'accès à root via su aux membres du groupe 'wheel'
auth	required	pam_wheel.so use_uid root_only
```

Dans le fichier `/etc/pam.d/passwd` :

```bash
# Au moins 12 caractères de 3 classes différentes parmi les majuscules ,
# les minuscules, les chiffres et les autres en interdisant la répétition
# d'un caractère

password required pam_pwquality.so minlen =12 minclass =3 \
              dcredit =0 ucredit =0 lcredit =0 \
              ocredit =0 maxrepeat =1
```

Dans les fichiers `/etc/pam.d/login` et `/etc/pam.d/sshd` :
```bash
# Blocage du compte pendant 5 min après 3 échecs
auth required pam_faillock.so deny=3 unlock_time=300
```

```bash
# On ajoute pam_mktemp pour fournir des répertoires temporaires privés par utilisateur
session	required	pam_mktemp.so umask=0077
# On ajoute pam_namespace pour créer un espace de noms privés par utilisateur
session	required	pam_namespace.so
```

### R69 : Sécuriser les accès aux bases utilisateur distantes
Lorsque les bases utilisateur sont stockées sur un serveur réseau distant, NSS doit être configuré pour établir une liaison sécurisée permettant au minimum d’authentifier le serveur et de protéger le canal de communication.

Le compte utilisé pour accéder à cette base de données doit être distinct des comptes utilisateur du système et il ne doit disposer que des droits strictement nécessaires pour interroger cette base de données.

### R70 : Séparer les comptes système et d'administrateur de l'annuaire
Il est recommandé de ne pas avoir de recouvrement de compte entre ceux utilisés par le système d’exploitation et ceux utilisés pour administrer l’annuaire.
L’usage de comptes administrateur d’annuaire pour effectuer des requêtes d’énumération de comptes par NSS doit être prohibé.

### R74 : Durcir le service de messagerie locale

Quand un service de messagerie est il doit être configuré pour n’accepter que :
- les messages à destination d’un compte utilisateur local à la machine ;
- les connexions sur l’interface réseau de la boucle locale, les connexions distantes
au service de messagerie doivent être rejetées.

Par défaut lors de l'installation d'un Debian minimal, aucun service de messagerie n'est installé ni en écoute.

Les services utilisant la messagerie sont souvent configurés par défaut pour envoyer des messages à destination du compte de l’administrateur système (ou root). Conformément à la recommandation R33, ce compte doit être désactivé. Par conséquent, et pour ne perdre aucun message adressé à l’administrateur système, l’utilisation d’ « alias » est recommandée.

Pour configurer un alias, ajouter dans le fichier `/etc/aliases` :

```bash
root: admin-gmorice
```

### R75 : Configurer un alias de messagerie des comptes de service
Pour chaque service exploité sur le système, ainsi que pour le compte d’administra-
teur (ou root), un alias vers un compte utilisateur administrateur doit être configuré
afin que celui-ci reçoive les notifications et les rapports adressés par messagerie élec-
tronique.

```bash
sudo nano /etc/aliases
```
Ajouter les lignes suivantes :

```bash
# Exemple d'alias pour les comptes de service
root: admin-gmorice
www-data: admin-gmorice
```

### R79 : Durcir et surveiller les services exposés

Les services exposés à des flux non maîtrisés doivent être durcis et particulièrement
surveillés. Cette surveillance consiste à caractériser le comportement du service, et à reporter tout écart par rapport à son fonctionnement nominal déduit des spécifications initiales attendues. 

Les services réseau sont souvent configurés par défaut en écoute sur l’ensemble des interfaces réseau, augmentant inutilement leur surface d’attaque.

Pour lister les services en écoute sur le système :

```bash
sudo ss -tuln
```

### Scan

Après application des recommandations de niveau M du guide, on éffectue un nouveau scan Lynis :

```bash
sudo lynis audit system
```

Voici les différences constatées :





## Recommandations R

### R1 : Choisir et configurer son matériel

Il est conseillé d’appliquer les recommandations du support matériel mentionnées dans la note technique « Recommandations de configuration matérielle de postes clients et serveurs x86 ».

### R7 : Activer l’IOMMU

Il est recommandé d’activer l’IOMMU en ajoutant la directive iommu=force à la liste des paramètres du noyau lors du démarrage en plus de celles déjà présentes dans les fichiers de configuration du chargeur de démarrage.

Pour modifier les paramètres de démarrage du noyau de manière permanente :

```bash
sudo nano /etc/default/grub
```

Ajouter à la fin de la ligne de la variable `GRUB_CMDLINE_LINUX_DEFAULT` le paramètre `iommu=force` :

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet iommu=force"
```

Mettre à jour la configuration de grub :

```bash
sudo update-grub
```

Redémarrer et vérifier que le paramètre est bien appliqué au démarrage en appuyant sur e lors de l'écran de démarrage grub puis en vérifiant la présence du paramètre à la fin de la ligne `linux   /boot/vmlinuz-[...]`:

```
sudo shutdown -r now
```

### R10 : Désactiver le chargement des modules noyau

Il est recommandé de bloquer le chargement des modules noyau par l’activation de l’option de configuration du noyau kernel.modules_disabled.

L’option de configuration du noyau permettant de bloquer le chargement des modules noyau est présentée telle que rencontrée dans le fichier de configuration `/etc/sysctl.conf` :

```bash
sudo nano /etc/sysctl.conf
```

```
# Interdit le chargement des modules noyau (sauf ceux déjà chargés à ce point)
kernel.modules_disabled=1
```

Appliquer les modifications :

```bash
sudo sysctl -p
```

Des modules noyau peuvent être définis pour être chargés au démarrage, avant que l’option de configuration du noyau Linux kernel.modules_disabled ne soit active. La liste est définie dans /etc/modules. La liste des modules chargés sur le système à un moment donné peut être trouvée avec la commande lsmod.

```bash
sudo lsmod
```

```
[sudo] password for admin-gmorice: 
Module                  Size  Used by
cfg80211             1392640  0
rfkill                 40960  2 cfg80211
8021q                  53248  0
garp                   16384  1 8021q
stp                    12288  1 garp
mrp                    20480  1 8021q
llc                    16384  2 stp,garp
binfmt_misc            28672  1
intel_rapl_msr         20480  0
intel_rapl_common      53248  1 intel_rapl_msr
intel_uncore_frequency_common    16384  0
intel_pmc_core        122880  0
intel_vsec             20480  1 intel_pmc_core
pmt_telemetry          16384  1 intel_pmc_core
[...]
```

Les modules et drivers actuellement listés étant essentiels au bon fonctionement du système nous ne retirerons pas de modules à ceux de la liste des modules chargés au démarrage.

### R29 : Restreindre les accès au dossier /boot

Lorsque c’est possible, il est recommandé de ne pas monter automatiquement la partition /boot.
Dans tous les cas, l’accès au dossier /boot doit être uniquement autorisé pour l’utilisateur root.

On applique les bonnes permissions à `/boot` :

```bash
sudo chmod 700 /boot
sudo chown root:root /boot
```

Idéalement, on ajouterait un paramètre au montage de `/boot` dans le fichier `/etc/fstab` pour rendre son montage non automatique au démarrage.

Ici, l'espace /boot est inclu dans le même système de fichiers que la racine `/` il n'y a donc pas possibilité de lui appliquer des paramètres différents.

### R36 : Modifier la valeur par défaut de UMASK

La valeur par défaut de UMASK des shells doit être positionnée à 0077 pour per mettre un accès au fichier ou au répertoire créé en lecture et écriture uniquement à l’utilisateur propriétaire. Celle-ci peut être spécifiée dans le fichier de configuration /etc/profile que la plupart des shells (bash, dash, ksh...) utiliseront.

```bash
sudo nano /etc/profile
```

Ajouter la ligne :

```
umask 0077
```

Puis se déconnecter et se reconnecter à la machine pour créer un nouveau shell appliquant le paramètre.

La valeur par défaut de UMASK des services doit être étudiée au cas par cas mais devrait généralement être à 0027 (ou plus restrictif). Ceci permet un accès au fichier ou au répertoire créé en lecture uniquement à l’utilisateur propriétaire et à son groupe et un accès en écriture au propriétaire. Pour les services systemd, cette valeur peut être spécifiée dans le fichier de configuration du service avec la directive UMask=0027.

Exemple sur un service systemd :

```bash
sudo nano /etc/systemd/system/example.service
```

Ajouter :

```
[Service]
UMask=0027
[...]
```

### R37 : Utiliser des fonctionnalités de contrôle d’accès obligatoire MAC

Il est recommandé d’utiliser les fonctionnalités de Mandatory Access Control ou contrôle d’accès obligatoire (MAC) en plus du traditionnel modèle utilisateur Unix, Discretionary Access Control ou contrôle d’accès discrétionnaire (DAC), voire éventuellement de les combiner avec des mécanismes de cloisonnement.

### R38 : Créer un groupe dédié à l’usage de sudo

Un groupe dédié à l’usage de sudo doit être créé. Seuls les utilisateurs membres de ce groupe doivent avoir le droit d’exécuter sudo.

Un cas envisageable est de créer un groupe dédié, par exemple sudogrp, et de lui attribuer les droits pour exécuter sudo.

Seuls les membres de ce groupe pourront ainsi y faire appel :

```bash
ls -al /usr/bin/sudo
-rwsr -x---. 2 root sudogrp [...] /usr/bin/sudo
```

Ici nous avons préalablement installé et configuré sudo pour être utilisable que par les membres du groupe `sudo` (groupe créé lors par défaut de l'installation du paquet). Nous avons ajouté les comptes nécéssitant des droits pour l'administration à ce groupe :

```bash
sudo usermod -aG sudo admin-gmorice
```

### R41 : Limiter l’utilisation de commandes nécessitant la directive EXEC

Les commandes nécessitant l’exécution de sous-processus, directive EXEC doivent être explicitement listées et réduites autant que possible au strict minimum.

### R45 : Activer les profils de sécurité AppArmor

Tout profil de sécurité AppArmor présent sur le système doit être activé en mode enforce par défaut quand la distribution en offre le support et qu’elle n’exploite pas de module de sécurité autre que AppArmor.

Sur les distributions GNU/Linux récentes, AppArmor est activé par défaut. Pour s’assurer que AppArmor est bien actif, utiliser aa-status :

```bash
sudo aa-status
apparmor module is loaded.
105 profiles are loaded.
6 profiles are in enforce mode.
23 profiles are in complain mode.
0 profiles are in prompt mode.
0 profiles are in kill mode.
76 profiles are in unconfined mode.
0 processes have profiles defined.
0 processes are in enforce mode.
0 processes are in complain mode.
0 processes are in prompt mode.
0 processes are in kill mode.
0 processes are unconfined but have a profile defined.
0 processes are in mixed mode.
```

Il faut notamment vérifier que les processus en cours d’exécution sur le système et pour lesquels un profil est déclaré soient confinés en mode enforce par AppArmor.

Installation de l'utilitaire de commande pour AppArmor :

```bash
sudo apt update
sudo apt install apparmor-utils
```

Activation et enforcement de tous les modules chargés par AppArmor :

```bash
sudo aa-enforce /etc/apparmor.d/*
```

Vérification :

```bash
sudo aa-status 
apparmor module is loaded.
105 profiles are loaded.
105 profiles are in enforce mode.
[...]
```

### R51 : Changer les secrets et droits d’accès dès l’installation

Tous les fichiers sensibles et ceux qui concourent aux mécanismes d’authentification doivent être mis en place dès l’installation du système. Si des secrets par défaut sont préconfigurés, ils doivent alors être remplacés pendant, ou juste après, la phase d’installation du système.

Voici quelques autres exemple ne s'appliquant pas dans notre cas (nouvelle installation sous Debian 13) :
* Comptes utilisateurs et mots de passe (déjà appliquée)
* Bases de données
* Fichiers de configuration de services sensibles
* Secrets d’applications (dans des fichiers de configuration par exemple)

Nous pouvons néanmoins renforcer l'accès par SSH avec une clé SSH non configurée par défaut :

Sur la machine d'administration / mgmt :

```bash
ssh-keygen -t rsa -b 4096
cat ~/.ssh/id_rsa.pub
```

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDhtS5b7V6wsenM9MIbG4fzUTAPjA2LvGzNZ43lFg6uW3eIEpd88FZwMTrXIN4fd4f+51iW8[...]
```

Sur la machine sur laquelle nous faisont le durcissement (et sur le compte d'administration correspondant : `admin-gmorice`):

```bash
mkdir ~/.ssh
chmod 700 ~/.ssh
touch ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
sudo nano ~/.ssh/authorized_keys
```

Coller la clé publique récupérée précédement.

Configurer le service SSH :

```bash
sudo nano /etc/ssh/sshd_config
```

Désactivation de l'authentification via le compte root

```bash
PermitRootLogin no
```

Désactivation de l'authentification par mot de passe

```bash
PasswordAuthentication no
```

Redémarrage du service SSH

```bash
sudo service sshd restart
```

Maintenant, nous pourrons nous connecter qu'à l'aide d'une clé SSH depuis ce compte. Également, les connexions au compte root sont maintenant désactivées rendant une escalade depuis un compte d'administrateur obligatoire.

### R57 : Éviter l’usage d’exécutables avec les droits spéciaux setuid root et setgid root

Les exécutables avec les droits spéciaux setuid root et setgid root doivent être le moins nombreux possible.
Lorsqu’il est attendu que seuls les administrateurs les exécutent, il faut leur retirer ces droits spéciaux (setuid ou setgid) et leur préférer les commandes comme su ou sudo, qui peuvent être surveillées.

### R60 : Utiliser des dépôts de paquets durcis

Lorsque la distribution fournit plusieurs types de dépôts, la préférence doit aller à ceux contenant des paquets faisant l’objet de mesures de durcissement supplémentaires. Entre deux paquets fournissant le même service, ceux faisant l’objet de mesures de durcissement (à la compilation, à l’installation ou dans la configuration par défaut) doivent être privilégiés.

Pour vérifier :

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

Nous utilisons les dépôts officiels pour la version Trixie de Debian 13.
Le gestionnaire de paquets a visibilité sur les paquets de la liste `main` mais également `non-free-firmware`. Ce dernier ne peut pas être retiré car il assure que certains drivers propriétaires soient présents sur le système et que les appareils correspondants soient bien reconnus et utilisables par le système.

### R64 : Configurer les privilèges des services

Tous les services et exécutables disponibles sur le système doivent faire l’objet d’une analyse afin de connaître les privilèges qui leur sont associés, et doivent ensuite être mis en œuvre et configurés en vue d’en utiliser le strict nécessaire.

Pour lister les services en cours de fonctionnement sur notre machine :

```
systemctl list-units --type=service --state=running
```

```
  UNIT                      LOAD   ACTIVE SUB     DESCRIPTION                                   
  cron.service              loaded active running Regular background program processing daemon
  dbus.service              loaded active running D-Bus System Message Bus
  getty@tty1.service        loaded active running Getty on tty1
  getty@tty6.service        loaded active running Getty on tty6
  ssh.service               loaded active running OpenBSD Secure Shell server
  systemd-journald.service  loaded active running Journal Service
  systemd-logind.service    loaded active running User Login Management
  systemd-timesyncd.service loaded active running Network Time Synchronization
  systemd-udevd.service     loaded active running Rule-based Manager for Device Events and Files
  user@1000.service         loaded active running User Manager for UID 1000
```

* cron.service

```bash
sudo service cron status
sudo nano /usr/lib/systemd/system/cron.service
```

```
# Ajouter à [Service] :
ProtectSystem=full
ProtectHome=yes
PrivateTmp=yes
```

```bash
sudo systemctl daemon-reload
sudo service cron restart
```

* dbus.service

```
PrivateTmp=yes
ProtectSystem=full
```

* getty@.service

```
ProtectSystem=yes
ProtectHome=yes
PrivateTmp=yes
```

* ssh.service

```
ProtectSystem=strict
ProtectHome=yes
PrivateTmp=yes
ProtectKernelTunables=true
ProtectKernelModules=true
ReadWritePaths=/var/run/sshd
CapabilityBoundingSet=CAP_NET_BIND_SERVICE
AmbientCapabilities=CAP_NET_BIND_SERVICE
```

* systemd-logind.service

```
ProtectSystem=yes
```

* systemd-timesyncd.service

```
CapabilityBoundingSet=CAP_NET_BIND_SERVICE
ProtectSystem=strict
ProtectClock=yes
PrivateTmp=yes
ProtectHome=yes
```

### R65 : Cloisonner les services

Dans le cadre du principe de minimisation, il est recommandé de cloisonner les services avec les mécanismes de confinement, de filtrage et d’isolation disponibles dans le noyau Linux.

Voici les configurations que nous pouvons ajouter à chaque services :

* ssh.service

```bash
sudo mkdir -p /etc/systemd/system/ssh.service.d/
sudo nano /etc/systemd/system/ssh.service.d/override.conf
```

```
[Service]
PrivateTmp=yes
ProtectSystem=strict
ProtectHome=yes
PrivateDevices=yes
CapabilityBoundingSet=CAP_NET_BIND_SERVICE
AmbientCapabilities=CAP_NET_BIND_SERVICE
SystemCallFilter=@system-service
ReadWritePaths=/var/run/sshd
NoNewPrivileges=true
```

```bash
sudo systemctl daemon-reload
sudo service ssh restart
```

* cron.service

```
[Service]
PrivateTmp=yes
ProtectSystem=full
ProtectHome=yes
NoNewPrivileges=true
SystemCallFilter=@system-service
```

* systemd-timesyncd.service

```
[Service]
PrivateTmp=yes
ProtectSystem=strict
ProtectHome=yes
CapabilityBoundingSet=CAP_NET_BIND_SERVICE
AmbientCapabilities=CAP_NET_BIND_SERVICE
ProtectClock=yes
NoNewPrivileges=true
```

* dbus.service

```
[Service]
PrivateTmp=yes
ProtectSystem=full
ProtectHome=yes
NoNewPrivileges=true
```

* getty@.service

```
[Service]
ProtectSystem=yes
ProtectHome=yes
PrivateTmp=yes
NoNewPrivileges=true
```

Parce que la machine en Debian "minimal" n’exécute que des services système essentiels directement intégrés au noyau et indispensables au fonctionnement de Debian, ils ne peuvent pas être déplacés ou virtualisés, et seul le cloisonnement interne via systemd (namespaces, cgroups, seccomp) est applicable.

On pourrait imaginer que les prochains services à ajouter au SI seront cloisonnés des autres par l'intermédiaire d'un hyperviseur ou des conteneurs.

### R71 : Mettre en place un système de journalisation

Il est d’usage d’avoir un serveur syslog résidant qui ne gère que les évènements soumis localement par les services du système et qui envoie éventuellement ces évènements à un serveur centralisé.

```bash
sudo apt update
sudo apt install rsyslog
sudo nano /etc/systemd/journald.conf
```

```
ForwardToSyslog=yes
```

```bash
sudo service systemd-journald restart
sudo chmod -R o-rwx /var/log
sudo chown -R root:root /var/log
sudo nano /etc/logrotate.d/rsyslog
sudo service rsyslog restart
```

Ici, nous enregistrons les logs du système dans le dossier /var/log en local sur la machie. Dans l'idéal et comme expliqué dans le guide, on utiliserait un serveur dédié à stocker les logs avec syslog-ng sur une infrastructure avec plusieurs serveurs. Chacun des journaux d'événements seraient envoyés au serveur via le protocol syslog en 514 ou 10514 via le service rsyslog en ajoutant une configuration dédiée.

### R72 : Mettre en place des journaux d'activité de service dédiés

Chaque service doit posséder un journal d’événements dédié sur le système. Ce journal ne doit être accessible que par le serveur syslog, et ne doit pas être lisible, modifiable ou supprimable par le service directement.

Dans notre cas l'objectif est donc d'obtenir les journaux d'événements suivants :
- /var/log/sshd.log
- /var/log/cron.log
- /var/log/timesyncd.log
- ...

```bash
sudo touch /var/log/sshd.log
sudo touch /var/log/cron.log
sudo touch /var/log/timesyncd.log
sudo chown root:root /var/log/*.log
sudo chmod 640 /var/log/*.log
sudo nano /etc/rsyslog.d/services.conf
```

```
# SSH
if ($programname == 'sshd') then /var/log/sshd.log
& stop

# cron
if ($programname == 'CRON') then /var/log/cron.log
& stop

# systemd-timesyncd
if ($programname == 'systemd-timesyncd') then /var/log/timesyncd.log
& stop
```

```bash
sudo systemctl restart rsyslog
```

Chaque futur service que l'on ajoute à la VM doit être journalisé par l'intermédiaire de rsyslog.

### R73 : Journaliser l’activité système avec auditd

La journalisation de l’activité du système doit être faite au travers du service auditd.

```bash
sudo apt update
sudo apt install auditd audispd-plugins
sudo nano /etc/audit/rules.d/hardening.rules
```

```
##############################
# Règles auditd - Conformité R73
# Debian 13 minimal
##############################

# ------------ MODULES Noyau ------------
# Exécution de insmod, rmmod et modprobe
-w /sbin/insmod -p x -k kernel-mod
-w /sbin/modprobe -p x -k kernel-mod
-w /sbin/rmmod -p x -k kernel-mod

# insmod, rmmod, modprobe → liens vers kmod
-w /bin/kmod -p x -k kernel-mod

# Chargement / suppression de modules (appels système)
-a always,exit -F arch=b64 -S init_module -S delete_module -S finit_module -k kernel-mod

# ------------ Fichiers système critiques ------------
# Modifications dans /etc
-w /etc/ -p wa -k etc-changes

# ------------ Appels système sensibles ------------
# Mount / Umount
-a always,exit -S mount -S umount2 -k mount

# Appels suspects x86 (toujours valables sur amd64)
-a always,exit -S ioperm -S modify_ldt -k suspicious

# Appels rares
-a always,exit -S get_kernel_syms -S ptrace -k rare-calls
-a always,exit -S prctl -k rare-calls

# ------------ Fichiers et répertoires (création / suppression) ------------
# ⚠️ Peut impacter les performances : OK pour Debian minimal
-a always,exit -F arch=b64 -S unlink -S rmdir -S rename -k fs-delete
-a always,exit -F arch=b64 -S creat -S open -S openat -F exit=-EACCES -k fs-perm
-a always,exit -F arch=b64 -S truncate -S ftruncate -F exit=-EACCES -k fs-perm

# ------------ Surveillance des services installés ------------
# SSH
-w /etc/ssh/ -p wa -k ssh-config
-w /usr/sbin/sshd -p x -k ssh-exec

# Cron
-w /etc/cron.d/ -p wa -k cron-edit
-w /etc/crontab -p wa -k cron-edit

# Timesyncd
-w /etc/systemd/timesyncd.conf -p wa -k time-config

# ------------ Verrouillage auditd ------------
# 2 = configuration non modifiable jusqu'au reboot
-e 2
```

```bash
sudo augenrules --load
sudo service auditd restart
sudo systemctl status auditd
sudo auditctl -l
```

Le système Debian 13 utilise auditd comme mécanisme principal de journalisation avancée conformément à la mesure R73. Des règles spécifiques surveillent les appels système critiques, les modifications de fichiers sensibles, les actions sur les modules noyau, les opérations de montage et les accès non autorisés. Les règles sont verrouillées (-e 2), garantissant que la configuration ne peut pas être modifiée hors redémarrage. Auditd complète la journalisation classique par syslog et journald, offrant une traçabilité approfondie adaptée à un système minimal sécurisé.

### R78 : Cloisonner les services réseau

### Scan

## Recommandations E

### R4 : Remplacer les clés préchargées

### R6 : Protéger les paramètres de ligne de commande du noyau et l’initramfs

### R15 : Paramétrer les options de compilation pour la gestion de la mémoire

### R16 : Paramétrer les options de compilation pour les structures de données du noyau

### R17 : Paramétrer les options de compilation pour l’allocateur mémoire

### R18 : Paramétrer les options de compilation pour la gestion des modules noyau

### R19 : Paramétrer les options de compilation pour les évènements anormaux

### R20 : Paramétrer les options de compilation pour les primitives de sécurité du noyau

### R21 : Paramétrer les options de compilation pour les plugins du compilateur

### R22 : Paramétrer les options de compilation pour la pile réseau

### R23 :Paramétrer les options de compilation pour divers comportements du noyau

### R24 : Paramétrer les options de compilation spécifiques aux architectures 32 bits

### R25 : Paramétrer les options de compilation spécifiques aux architectures x86_64 bits

### R26 : Paramétrer les options de compilation spécifiques aux architectures ARM

### R27 : Paramétrer les options de compilation spécifiques aux architectures ARM 64 bits

### R46 : Activer SELinux avec la politique targeted

### R47 : Confiner les utilisateurs interactifs non privilégiés

### R48 : Paramétrer les variables SELinux

### R49 : Désinstaller les outils de débogage de politique SELinux

### R66 : Durcir les composants de cloisonnement

### R76 : Sceller et vérifier l’intégrité des fichiers

### R77 : Protéger la base de données des scellés

## Sources

- [Recommandations de sécurité relatives à un système GNU/Linux](https://cyber.gouv.fr/publications/recommandations-de-securite-relatives-un-systeme-gnulinux)
