
ADMINISTRATION LINUX - sujet 1
=================

# PLANIFICATION DE TÂCHE

## Introduction
CRON est un daemon qui permet d’automatiser l'exécution de programmes ou de scripts de manière récurrente (tous les jours par exemple) ou à une date prédéfinie (par exemple le 1 avril à 10h00).


### Prérequis:
Nous allons nous assurer que votre machine soit correctement configurée pour récupérer l’heure et la date depuis un serveur ntp:


1. Vérifiez que vous possédez les dernières versions de ntp, ntpq (ntp query) et ntpdate (mise à jour de l’horloge)
```bash
apt-get update
apt install ntp ntpq ntpdate
```



2. Vérifiez l’état du service ntp
```bash
service ntp status
```

3. Vérifiez l’état des serveurs ntp
     Les serveurs sont mentionnés dans votre fichier /etc/ntp.conf.
```bash
ntpq -p
```

4. Par défaut, votre système utilisera l’heure universelle (UTC). Lancez timedatectl.
```bash
timedatectl
```
La ligne Time zone: Etc/UTC (UTC, +0000)vous indique que l’heure système est basé sur l’heure universelle. Il faudra changer de fuseau horaire pour passer à l’heure française.


5. Pour changer de fuseau horaire (Europe/Paris)
```bash
timedatectl set-timezone Europe/Paris
```

7. Relancez timedatectl et vérifiez que fuseau horaire soit bien celui de Paris  Time zone: Europe/Paris (CET, +0100)


## Utilisation du daemon cron
Fonctionnement
Les tâches planifiées sont définies à partir de fichiers crontab. Chaque utilisateur à la possibilité de planifier une tâche avec la commande crontab, qui créera un crontab dédié à cet l'utilisateur.  Ces fichiers crontab sont stockés dans le répertoire  /var/spool/cron/crontabs/<utilisateur> pour tous les utilisateurs  (root inclus). Notez qu’il existe un /etc/crontab qui est exclusivement réservé pour les tâches système du daemon cron; donc, nous ne l'utiliserons pas.


### La commande crontab
crontab est la commande qui va nous permettre d’ajouter, supprimer ou lister les entrées des tables utilisées par le démon cron.


Liste non exhaustives des options de crontab
| Commande | Description|
|--|--|
| crontab -e  | Édite le fichier crontab de l’utilisateur /var/spool/cron/crontab/<user>. Il sera créé s’il n’existe pas. |
|-crontab -l | Affiche le fichier /var/spool/cron/crontab/<votre utilisateur>. |
| crontab -r | Supprime le fichier  /var/spool/cron/crontab/<votre utilisateur> |
| crontab -u <utilisateur> | Permute d’utilisateur pour le fichier crontab. Il faudra ajouter l’option que vous désirez à la suite.  Cette option nécessite des privilèges root.
 |

> Pour la liste complète faite `man crontab`


## Travaux pratiques 1
1. Utilisez la commande crontab pour créer votre fichier crontab. Lors de la première exécution de crontab vous demandera de choisir un éditeur de texte, choisissez nano.
2. Supprimez tous les commentaires
3. Remplacez ceci (faites un copier/coller), enregistrez et quittez.
```bash
\# Fichier crontab
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
# Exemple de planification de tache:
# .---------------- Minute (0 - 59)
# |  .------------- Heure (0 - 23)
# |  |  .---------- Jour du mois (1 - 31)
# |  |  |  .------- Mois (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- Jour de la semaine (0 - 6) oubien sun, mon, tue, wed ...
# |  |  |  |  |
# *  *  *  *  * commande
```
4. Créez un nouvel utilisateur “user1”
5. Loguez vous avec cet utilisateur “user1”
6. Utilisez `crontab -e` pour éditer le fichier crontab de l’utilisateur “user1”
7. Supprimez tous les commentaires
8. Ajoutez la ligne:
```
# Crontab de l’utilisateur user1”
```
et enregistrez vos modifications
9. Reconnectez-vous avec root
10. Rendez-vous dans le répertoire /var/spool/cron/crontab
11. Quels fichiers sont présents ?
12. Quels sont leurs contenus ?

### Les variables CRON
$PATH : La variable PATH définit le chemin par défaut où se trouve les commandes que vous souhaitez exécuter. Si la commande que vous exécutez n'est pas présente dans le PATH, votre tâche ne sera pas exécutée. Pour régler cela vous pouvez soit :
* utiliser le chemin absolu de la commande
* modifier la variable cron PATH.


**$SHELL**: Définit le shell que cron devra utiliser pour exécuter vos tâches. Il en existe plusieurs (sh, bash ,ksh, etc ...). Nous n'utilisons pas de shell spécifique pour ce TP. Lancez les valeurs par défaut.


**$HOME**: La variable $HOME peut être définie dans crontab si vous avez besoin de mentionner un répertoire de travail spécifique. Nous n'utilisons pas cette variable dans ce TP.


**$MAILTO** : permet la notification par e-mail.Il est possible de mentionner liste de destinataire en séparant par des virgules les adresses. Dans ce TP MAILTO sera défini vide (MAILTO = ""), aucun mail ne sera envoyé.


### Les tâches planifiées
Pour afficher la liste des tache faite crontab -l.


Chaque ligne représente une tâche et procèdent 6 paramètres séparés d’un espace:
1. Les minutes
2. Les heures
3. Le jour
4. Le mois
5. Le jour de la semaine
6. La commande a exécuter


Voici un exemple où root exécute le script full-backup.sh à 23:30 le 15 mai :
30 23 15 05* /root/scripts/full-backup.sh
* 30 – la 30ème minute
* 23– de la 23ème heure
* 15– du 15ème jour
* 05– du 5ème mois (soit mai)
* – n’importe quel jour de la semaine

Les  différents paramètres:
Position
	Paramètres
	Valeurs possibles
	1er champs
	Les minutes
	0-59
	2ème champs
	Les heures
	0-23
	3ème champs
	Le jour du mois
	0-31
	4ème champs
	Le mois
	1-12 ou
jan, fev, mar, apr, may, jun, etc...
	5ème champs
	Le jour de la semaine
	0-6 où 0=dimanche
sun, mon, tue, wed, thu, fri, sat
	6ème champs
	Commande
	Vous devez  respecter la syntaxe du SHELL définit dans la variable $SHELL. Pour nous ce sera sh. Sont autorisés les pipelines et redirections.


Travaux pratiques 2


Pour chaque champs (unité de temps), minute, heure, jour, mois et jour de la semaine, on peut utiliser :


1. Une valeur afin définir une date et/ou une horaire
Exemple1
30 23 15 05 * /root/scripts/full-backup.sh
30 → la 30ème minute
23 → de la 23ème heure
15 → du 15ème jour
05 → du 5ème mois (soit mai)
* → peu importe le  jour de la semaine


Soit à 23h30 le 15 mai


Travaux pratiques: Faite la même chose pour mais pour le 10 septembre à 8h30


Exemple 2
30 23 * * mon /root/scripts/full-backup.sh
30 → la 30ème minute
23 → de la 23ème heure
* → tous les jours
* → tous les mois
mon → le lundi


Soit à 23h30 tous les lundis, puisque l’on a définit quelques que soit les jours et le mois


Travaux pratiques: Faite la même chose mais pour les mardis


2. Un astérix (*) indique toutes les unités de temps. C’est à dire toutes les minutes si un astérix est positionné dans le champs minutes, toutes les heures si un * est positionné dans le champ heure, etc…


Exemple 3
0 8 * * * /root/scripts/launch.sh
0 → la 30ème minute
8→ de la 23ème heure
* → tous les jours
* → tous les mois
* → peu importe le  jour de la semaine


Soit à 8h00 tous les jours


Travaux pratiques: Faite la même chose mais à 18h15


3. La virgule permet de définir plusieurs valeurs distinctes. Par exemple: 14,18 positionné dans le champ heure exécutera la commande à 14 et 18 heures.


Exemple 4
0 14,18 * * * /root/scripts/launch.sh
0 → la 0ème minute, soit à pile
14,18 → de la 14ème et la 18ème heure
* → tous les jours
* → tous les mois
* → peu importe le  jour de la semaine


Soit à 14h et 18h tous les jours


Travaux pratiques: Faite la même chose mais à 14h les lundi et mardi
4. Le tiret du 6 (-) pour définir une plage d’unité de temps. Par exemple 1-5 est équivalent à 1,2,3,4,5. La commande sera exécutée à chaque unité de temps inclus dans la plage concernée.


Exemple 5
0 23 * * * /root/scripts/check.sh
0 → la 30ème minute
23 → de la 23ème heure
* → tous les jours
1-6  → tous les mois de janvier à juin
* → peu importe le  jour de la semaine


Soit à 23h tous les jours du mois de janvier au mois de juin


Travaux pratiques: Faite la même chose du mois de juillet à décembre


5. / la barre oblique permet de définir une intervalle d’unité de temps. Par exemple */15 positionné sur le champs minutes, exécutera la commande toute les 15 minutes. */6 positionné sur les mois exécutera la commande tous les 6 mois.


Exemple 6
*/15 * * * * /root/scripts/check_mail.sh
*/15 → prend toutes les valeurs avec une intervalle de 15 minutes
* → toutes les heures
* → tous les jours
* → tous les mois
* → peu importe le  jour de la semaine


Soit toutes les 15 minutes


Travaux pratiques: Faite la même chose toutes les 10 jours à 8h00


Notez qu’il existe des raccourcis:
* @reboot                 Lancement une fois au démarrage.
* @yearly                 Lancement une fois par an (remplace: 0 0 1 1 *)
* @monthly                 Lancement une fois par mois (remplace: 0 0 1 * *)
* @weekly                 Lancement une fois par semaine (remplace: 0 0 * * 0)
* @daily                 Lancement une fois par jour (remplace: 0 0 * * *)
* @hourly                 Lancement une fois par heure (remplace: 0 * * * *)






Exemple 7
0 8,17 * */3 mon,fri /root/scripts/check_mail.sh
0 → la 0ème minute
8,17 → La 8ème et 17ème heure
* → Peut importe le jour
*/3 → tous les 3 mois
mon,fri → Uniquement les lundi et vendredi


Soit à 8h et 17h tous les 3 mois uniquement les lundis et vendredi


Exercices
Créer le script tp_cron1.sh
nano tp_cron1.sh


Ce script affiche la date et l’heure comme ceci: “13/02/21 09:44:27”
#!/bin/bash
#
# Nom du script: tp_cron1.sh


date "+%d/%m/%y %H:%M:%S"


Pensez à ajouter les droits en exécution à votre script.
chmod +x tp_cron1.sh


1. Ajoutez une tache qui exécutera le script tp-cron.sh toutes les minutes


2. Ajoutez tâche qui exécutera le script tp-cron.sh à 10, 25 et 45 minutes


3. Ajoutez tâche qui exécutera le script tp-cron.sh à 10h15 tous les jours


4. Ajoutez une tache qui exécutera le script tp-cron.sh tous les jours de 8 à 17h heure.


5. Ajoutez une tache qui exécutera le script tp-cron.sh toute la journée 1 heure sur 2


6. Sans utiliser le raccourci @monthly lancez  le script tp-cron.sh une fois par mois


7. Lancez le script tp-cron.sh tous les 3 jours.


8. Ajouter une tâche qui exécute apt-get update tous les lundi


9. Exécutez le script tp-cron.sh une fois par trimestre.


10. Envoyez un message aux utilisateurs avec  la commande wall “Message URGENT: C’est bientôt la pause” tous les jeudis à 12h30 de septembre à juin.


11. Planifiez une (pseudo) sauvegarde à partir du script sauvegarde.sh tous les jours sauf les samedis et dimanches à 22h00.


12.  une (pseudo) sauvegarde à partir du script sauvegarde.sh tous les lundi les mois de septembre, octobre, novembre, décembre, janvier, février et mars


## Les logs CRON

### Logger les erreurs


> **Rappels sur les redirections**
>	Redirige la sortie standard et la concatène avec le contenu du fichier fichier.log
>	\>\> fichier.log
	Redirige la sortie standard et écrase le fichier fichier.log
	2> fichier.log
	Redirige la sortie des erreurs vers le fichier fichier.log


**Exemple**
```bash
* * * * * /home/user/myscript.sh >/home/user/myscript.log 2>&1
```
> `... \> /home/user/myscript.log` indique à cron de rediriger la sortie standard (STDOUT) vers le fichier /home/user/myscript.log .
>
> Notez que STDOUT sera ajouté e fin de fichier. `myscript.log` ne sera pas écrasé.
>
> `2>&1` indique à cron d'envoyer les erreurs (STDERR) vers  la même cible que la sortie normale (STDOUT)

13. Reprenez votre tache de l'exercice 12 et redirigez la sortie standard vers le fichier `/var/log/mon_cron.log`

14. Reprenez votre tache de l'exercice 12 et redirigez la sortie erreur vers le fichier `/var/log/erreurs_cron.log`


## Log système
Le daemon CRON laisse des traces dans le log système /var/log/syslog :
```bash
cat /var/log/syslog
```
Sans surprise, ce log est très chargé.


Si vous souhaitez filtrer ce fichier de logs et afficher uniquement les lignes impliquant CRON, faite:
```bash
grep CRON /var/log/syslog
```

## Gérer les permissions d’exécution de CRON
Par défaut les distributions Debian (et dérivés) autorisent tous les utilisateurs à planifier une tâche avec CRON. Si besoin, il est possible de filtrer les utilisateurs ayant le droit d'accéder au daemon CRON.


* Si le fichier /etc/cron.allow existe, vous devez y figurer (un utilisateur par ligne) pour être autorisé à utiliser crontab.
* Si le fichier /etc/cron.allow n’existe pas mais que /etc/cron.deny existe, vous ne devez pas y être répertorié. Faute de quoi, votre utilisateur ne sera plus autorisé à utiliser crontab.
* Si les 2 fichiers /etc/cron.allow et /etc/cron.deny existent, le fichier /etc/cron.allow sera prioritaire et /etc/cron.deny ignoré.
* Si aucun de ces fichiers ne sont présent, par défaut Debian autorise tous les utilisateurs à utiliser crontab.


15.  A l’aide du fichiers cron.allow, autorisez uniquement root à planifier une tâche


16. Créer un second utilisateur “user2”. Loguez-vous avec l’utilisateur user2. Essayez de planifier une tâche. Quel résultat obtenez-vous ?


17. Reconnectez-vous avec root. Ajoutez “user2” comme utilisateur autorisé à planifier une tâche.

18. Loguez-vous avec l’utilisateur user2.

19. Essayez de planifier une tâche. Quel résultat obtenez-vous ?
