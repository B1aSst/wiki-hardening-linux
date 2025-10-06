# Recommendation de configuration pour AppArmor

## Fonctionnement

AppArmor est un *système de contrôle d'accès obligatoire (Mandatory Access Control)* qui s'appuie sur l'interface Linux Security Modules fournie par le noyau Linux. Concrètement, le noyau interroge AppArmor avant chaque appel système pour savoir si le processus est autorisé à effectuer l'opération concernée. Ce mécanisme permet à AppArmor de confiner des programmes à un ensemble restreint de ressources.

On peut configurer des profils AppArmor sur différent modes:
- **complain**: une violation de la règle sera juste indiquée dans les logs.
- **enforce**: une violation de la règle entrainera un blocage de l'appel système.

Il est important de noter que les règles "deny" sont bloquées même en mode "complain".

A partir de la version 10 "Buster" de Debian, AppArmor est activé par défaut.

## Vérifier l'état

Afin de vérifier si AppArmor est activé, on peut utiliser la commande suivante qui doit retourner Y.

```sh
cat /sys/module/apparmor/parameters/enabled
```

Afin de lister les profils pour les applications et processus en détail:
```sh
sudo aa-status
```

## Sources

- https://debian-handbook.info/browse/fr-FR/stable/sect.apparmor.html
- https://wiki.debian.org/AppArmor/HowToUse
