# Introduction

Ce Wiki présentera le durcissement effectué sur un système d'exploitation Debian 13 en suivant principalement les recommendations de l'[ANSSI](https://cyber.gouv.fr/) ainsi que d'autres sources comme Debian. Il explorera la configuration que ce soit du système d'exploitation ou de service omniprésent sur les serveurs comme OpenSSH. Le but est ici est d'améliorer le score de durcissement tout en conservant un fonctionnement normal du système.

## Spécification

Dans le cadre de ce durcissement, une machine virtuelle Debian 13 "Trixie" sera utilisée avec les spécifications suivantes :

* 2 CPU
* 4 Go de RAM
* 25 Go de stockage

## Installation

L'hyperviseur VirtualBox sera utilisé pour la machine virtuelle mais n'importe quel autre hyperviseur aurait pu être utilisé.

On télécharge le dernier ISO sur le site officiel de Debian : [Debian.org](https://www.debian.org/download.fr.html)

On suit le script d'installation et on choisis les options suivantes :

- Partition LVM chiffrée
- Installation paquet pour le service OpenSSH
