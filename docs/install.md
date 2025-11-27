# Installation

Le système d'exploitation utilisé pour l'hardening sera Debian 13 (Trixie). Il sera installé sur l'hyperviseur VirtualBox.

Les spéfications utilisées pour la VM seront les suivantes :
- 2 CPU
- 4Go de RAM
- 25Go de disque

Elles seront suffisante du fait qu'aucune interface graphique ne sera installée.

Lors de l'installation, on choisis les options suivantes :
- partitionnement LVM chiffré
- paquet pour serveur SSH

Un fois l'installation terminée, on prend un premier snapshot avant de commencer le durcissement

# Hardening

## Après installation

Un premier test va être effectué directement après installation sans la mise à jour du système. Un premier outil qui est Debsecan va nous permettre de remonter la liste des CVE sur les paquets actuellement installés sur le système. On élève nos privilège et on lance la commande en tant que "root".

```bash
apt install debsecan
debsecan --suite $(lsb_release --codename --short) --only-fixed --format detail
```

La commande ne donne rien car il semble que tout les paquets aient déjà été mis à jour lors de l'installation :

On va maintenant utiliser l'outil Lynis qui va nous permettre d'auditer le système :

```bash
apt install lynis
lynis audit system
```

On va maintenant installer le paquet sudo et placer notre utilisateur dans le groupe sudo, c'est une meilleure pratique car il n'est pas recommandé d'exécuter des commandes en tant qu'utilisateur root.

```bash
apt install sudo
usermod -aG sudo <user>
```
