# Recommendation de configuration pour un usage sécurisé d'OpenSSH

Le protocole SSH est particulièrement utilisé afin d'administrer des serveurs à distances car il utilise des protocoles cryptographiques pour sécurisé la communication avec le serveur.

Nous allons dans ce wiki présenter les recommandations pour le durcissement du service OpenSSH qui implémente le protocole OpenSSH.

Chaque modification sera expliqué est présenté dans quel contexte il est le plus important.

Afin d'appliquer les recommendations, on va principalement travailler dans le fichier de configuration du service "/etc/ssh/sshd_config". Afin que le service prenne en compte les changements, il est nécessaire de le redémarrer :

```sh
sudo systemctl restart sshd.service
```

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
StrictHostKeyChecking ask
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

## Durcissement système

### Séparation des priviléges

Afin de réduire les droits au strict nécessaire :

```sh
UsePrivilegeSeparation sandbox
```

## Authentification

### Authentification d'un utilisateur

Il est intéressant de si possible, configurer sur le serveur une liste d'accés pour les clients ayant le droits de se connecter. Le fichier `authorized_keys` permet cela:

```sh
# Autorise les clients du sous-réseau "192.168.10.0/24" à se connecter avec la clé privé correspondante
from="192.168.10.*" ssh-ecdsa <clé-publique-client>
# Pour bloquer l'agent forwarding de cette clé
no-agent-forwarding ssh-ecdsa <clé-publique-client>
```

On peut choisir d'indiquer à partir de combien d'échecs de connexions, l'échec doit être log ainsi que le temps au bout du quel le serveur se déconnecte si l'utilisateur ne s'est pas authentifié.

```sh
MaxAuthTries 2
LoginGraceTime 30
```

Dans un réseau non maîtrisé susceptible d'être scanné et brute force par des bots, il est préférable d'autoriser une authentification uniquement par clé et non par mot de passe :

```sh
PubkeyAuthentication yes
PasswordAuthentication no
```

### Imputabilité des accès

La possibilité de se connecter à un compte sans mot de passe doit être interdite afin d'éviter de potentiel pivotement. La connexion en tant que root doit être interdite et les informations de dernière connexion sont préférables d'être affichées.

```sh
PermitEmptyPasswords no
PermitRootLogin no
PrintLastLog yes
```

Il est possible de également de restrindre la connexion pour des utilisateurs ou groupes en particulier:

```sh
# Restreint 'admin' aux adresses IPs d'un réseau spécifique
AllowUsers admin@192.168.10/24
# Restreint le groupe 'sudo' à se connecter depuis un réseau spécifique
AllowGroups sudo@192.168.20/24
```

Bloquer la modification de l'environnement utilisateur par le service sshd

```sh
PermitUserEnvironment no
```

## Protocole et accès réseau

Sur un réseau maîtrisé, le serveur SSH doit écouter uniquement sur une interface du réseau d'administration.

```sh
Port 22
ListenAddress A.B.C.D
```

### Forwarding

Si le serveur n'est pas spécifiquement configuré en tant que bastion d'administration, il est préférable de désactiver le forwarding TCP:

```sh
AllowTcpForwarding no
```

De la même manière, si il n'y a pas un besoin spécifique, le forwarding X11 doit être désactivé:

```sh
ForwardX11Trusted no
```

## Sources

- [ANSSI - Usage sécurisé d’(Open)SSH](https://cyber.gouv.fr/publications/usage-securise-dopenssh)
