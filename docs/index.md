# Introduction

Ce Wiki présentera le durcissement effectué sur un système d'exploitation Debian 13 en suivant principalement les recommendations de l'[ANSSI](https://cyber.gouv.fr/). Il explorera la configuration que ce soit du système d'exploitation ou de service omniprésent comme OpenSSH. Le but est ici est d'améliorer le score de durcissement tout en conservant un fonctionnement normal de notre système.

## Spécification

Pour commencer, nous allons installer Debian 13 “Trixie” sur l’hyperviseur de VirtualBox. Pour notre machine virtuelle, nous avons utilisé les spécifications suivantes :

* 2 CPU
* 4 Go de RAM
* 25 Go de stockage

Cela devrait être amplement suffisant du fait qu’il n’y aura pas d’interface graphique. Lors de l’installation, nous avons créé une partition LVM chiffré et avons ajouté le paquet pour le service OpenSSH. Après ça, on peut passer à la phase de durcissement.
