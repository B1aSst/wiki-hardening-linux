# Recommendation de configuration pour AppArmor

## Fonctionnement

AppArmor est un *système de contrôle d'accès obligatoire (Mandatory Access Control)* qui s'appuie sur l'interface Linux Security Modules fournie par le noyau Linux. Concrètement, le noyau interroge AppArmor avant chaque appel système pour savoir si le processus est autorisé à effectuer l'opération concernée. Ce mécanisme permet à AppArmor de confiner des programmes à un ensemble restreint de ressources.

On peut configurer des profils AppArmor sur différent modes :

- **"complain"** : une violation de la règle est seulement enregistrée dans les logs, sans blocage.
- **"enforce"** : une violation de la règle entraîne le blocage de l’appel système.

Il est important de noter que les règles "deny" sont bloquées même en mode "complain".

*A partir de la version 10 "Buster" de Debian, AppArmor est activé par défaut.*

## Configuration

### Installation des paquets

```sh
sudo apt install apparmor apparmor-utils
```

### Status

Afin de vérifier si AppArmor est activé, on peut utiliser la commande suivante qui doit retourner Y.
```sh
cat /sys/module/apparmor/parameters/enabled
```

Afin de lister les profils pour les applications et processus en détail :
```sh
sudo aa-status
```

On peut voir les éxécutables qui sont confinés :
```sh
ps auxZ | grep -v '^unconfined'
```

On peut également voir ceux qui ont des ports utilisés mais qui n'ont pas de profil AppArmor :
```sh
sudo aa-unconfined
```

### Ajouter des profils

Il est possible d'ajouter des profils pour d'autres éxécutables. Le paquet `apparmor-profiles-extra` permet de télécharger des profils AppArmor spécifique à Debian et `apparmor-profiles`, des profils expérimentaux.

Pour voir les nouveaux profils :
```sh
/usr/share/apparmor/extra-profiles/
```

Installer un profil :
```sh
sudo cp /usr/share/apparmor/extra-profiles/usr.sbin.sshd /etc/apparmor.d/
```

Activer un profil en mode "complain" ou "enforce" (ici pour le service serveur ssh):
```sh
sudo aa-complain /etc/apparmor.d/usr.sbin.sshd
sudo aa-enforce /etc/apparmor.d/usr.sbin.sshd
```

## Sources

- [Introduction à AppArmor](https://debian-handbook.info/browse/fr-FR/stable/sect.apparmor.html)
- [Debian AppArmor - How to use)](https://wiki.debian.org/AppArmor/HowToUse)
- [Sécuriser Linux simplement avec AppArmor](https://blog.stephane-robert.info/docs/securiser/durcissement/apparmor/)
