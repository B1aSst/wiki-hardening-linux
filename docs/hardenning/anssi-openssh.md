# OpenSSH

Le protocole SSH est particulièrement utilisé afin d'administrer des serveurs à distances car il utilise des protocoles cryptographiques pour sécurisé la communication avec le serveur.

Nous allons dans ce wiki présenter les recommandations pour le durcissement du service OpenSSH qui implémente le protocole OpenSSH.

Chaque modification sera expliqué est présenté dans quel contexte il est le plus important.

Afin d'appliquer les recommendations, on va principalement travailler dans le fichier de configuration du service "/etc/ssh/sshd_config".

### Version

Le protocole SSH qui doit être utilisé est la version 2 car la version 1 est aujourd'hui obsolète.
Pour forcer la version 2, on ajoute le paramètre "Protocol 2" dans le fichier de configuration :

```sh
Protocol 2
```

## Cryptographie

### Authentification

L'authentification SSH se repose sur la cryptographie asymétrique.
