# Recommendation de configuration pour un usage sécurisé d'OpenSSH

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

L'authentification SSH se repose sur la cryptographie asymétrique. Afin de s'assurer de la légitimité du serveur, on peut demander au client la validation de la clé hôte via le paramètre :

```sh
StricHostKeyChecking ask
```

### Génération de clés - tailles et algorithmes

Il est recommandé d'utiliser des *clés ed25519* avec une taille de clé minimale de 256 bits. Si la paire de clé n'est pas généré dans le dossier "/etc/ssh", elle peut être généré avec la commande :

```sh
ssh-keygen -t ed25519 -b 256 -f ssh_host_ed25519_key
```

On peut ensuite spécifier que l'on veut utiliser la clé privée pour l'authentification avec le paramètre :

```sh
HostKey /etc/ssh/ssh_host_ed25519_key
```

### Droits d'accès et diffusion des clés

Il est impératif de vérifier que la clé privé n'est lisible que par le service sshd et root.

```sh
-rw------- 1 root root    411 30 sept. 11:00 ssh_host_ed25519_key
-rw-r--r-- 1 root root    100 30 sept. 11:00 ssh_host_ed25519_key.pub
```

Dans le cas contraire, les droits doivent être modifiés.

Il est important de vérifier que le client a les bons droits utilisateur sur sa clé privée. On peut ajouter le paramètre suivant :

```sh
StrictModes yes
```
