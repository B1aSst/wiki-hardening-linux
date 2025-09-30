# Hardenning

## Introduction

Nous allons d’abord vérifier l’état de sécurité de la machine après l’installation et sans mise à jour.

Pour ce faire, nous allons commencer par utiliser Debsecan qui va nous remonter la liste des CVE sur les paquets actuellement installé sur le system.

Après avoir élevé nos privilèges en tant que “root“ nous exécutons cette commande :

```bash
sudo apt update && apt install debsecan && debsecan --suite $(lsb_release --codename --short) --only-fixed --format detail
```

Cette commande ne nous donne rien car les paquets ont l'air d'être déjà à jours dès l’installation.

On va maintenant utiliser l'outil Lynis qui va nous permettre d'auditer le système. Pour ce faire, nous utilisons cette commande :

```bash
sudo lynis audit system
```
