# Recommandations de configuration d'un système GNU/LINUX - Guide ANSSI

Source : [Recommandations de sécurité relatives à un système GNU/Linux](https://cyber.gouv.fr/publications/recommandations-de-securite-relatives-un-systeme-gnulinux)

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

### R58 : N’installer que les paquets strictement nécessaires

### R59 : Utiliser des dépôts de paquets de confiance

### R61 : Effectuer des mises à jour régulières

### R62 : Désactiver les services non nécessaires

### R68 : Protéger les mots de passe stockés

### R80 : Réduire la surface d’attaque des services réseau

### Scan

## Recommandations I

### R2 : Configurer le BIOS/UEFI
