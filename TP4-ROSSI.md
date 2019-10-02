# ROSSI Thomas

# TP 4 - Utilisateurs, groupes et permissions

## Exercice 1. Gestion des utilisateurs et des groupes

**Commencez par créer deux groupes groupe1 et groupe2**

```
groupadd groupe1
groupadd groupe2

thomas@ubuntu-server:~$ cat /etc/group | tail -2
groupe1:x:1001:
groupe2:x:1002:
```

Les groupes ont donc bien été créés

**Créez ensuite 4 utilisateurs u1, u2, u3, u4 avec la commande useradd, en demandant la création de
leur dossier personnel et avec bash pour shell**

```
useradd -mksd u1
useradd -mksd u2
useradd -mksd u3
useradd -mksd u4
```

**Placez les utilisateurs dans les groupes :**
- u1, u2, u4 dans groupe1
- u2, u3, u4 dans groupe2

```
usermod -a -G groupe1 u1
usermod -a -G groupe1 u2
usermod -a -G groupe1 u4
usermod -a -G groupe2 u2
usermod -a -G groupe2 u3
usermod -a -G groupe2 u4
```

**Donnez deux moyens d’afficher les membres de groupe2**

```
grep groupe2 /etc/group
members groupe2
```

**Faites de groupe1 le groupe propriétaire de /home/u1 et /home/u2 et de groupe2 le groupe propriétaire
de /home/u3 et /home/u4**

```
chown :groupe1 u1 u2
chown :groupe2 u3 u4

root@ubuntu-server:/home# ls -l
total 20
drwxr-xr-x 8 thomas thomas  4096 sept. 30 14:30 thomas
drwxr-xr-x 2 u1     groupe1 4096 sept. 30 14:06 u1
drwxr-xr-x 2 u2     groupe1 4096 sept. 30 14:07 u2
drwxr-xr-x 2 u3     groupe2 4096 sept. 30 14:14 u3
drwxr-xr-x 2 u4     groupe2 4096 sept. 30 14:07 u4
```

**Remplacez le groupe primaire des utilisateurs :**
- groupe1 pour u1 et u2
- groupe2 pour u3 et u4

```
root@ubuntu-server:/home# usermod u1 -g groupe1
root@ubuntu-server:/home# usermod u2 -g groupe1
root@ubuntu-server:/home# usermod u3 -g groupe2
root@ubuntu-server:/home# usermod u4 -g groupe2
```

**Créez deux répertoires /home/groupe1 et /home/groupe2 pour le contenu commun aux groupes, et
mettez en place les permissions permettant aux membres de chaque groupe d’écrire dans le dossier
associé.**

```
mkdir groupe1 groupe2

chown :groupe1 groupe1
chown :groupe2 groupe2

chmod g+w groupe1
chmod g+w groupe2
```

**Comment faire pour que, dans ces dossiers, seul le propriétaire d’un fichier ait le droit de renommer
ou supprimer ce fichier ?**

```
chmod 700 groupe1
chmod 700 groupe2
```

**Pouvez-vous vous connecter en tant que u1 ? Pourquoi ?**

Non je ne peux pas car je n'ai pas défini de mot de passe.

**Activez le compte de l’utilisateur u1 et vérifiez que vous pouvez désormais vous connecter avec son
compte.**

sudo passwd u1

Et la connexion foonctionne bien

**Quels sont l’uid et le gid de u1 ?**

```
thomas@ubuntu-server:/home$ id u1
uid=1001(u1) gid=1001(groupe1) groups=1001(groupe1)
```

**Quel utilisateur a pour uid 1003 ?**

```
root@ubuntu-server:/home# grep 1003 /etc/passwd
u4:x:1003:1002::/home/u4:/bin/sh
```
C'est u4

**Quel est l’id du groupe groupe1 ?**

```
root@ubuntu-server:/home# grep groupe1 /etc/group
groupe1:x:1001:u1,u2,u4
```

l'id est de groupe1 est 1001

**Quel groupe a pour guid 1002 ?**

```
root@ubuntu-server:/home# grep 1002 /etc/group
groupe2:x:1002:u4,u3,u2
```

C'est groupe 2

**Retirez l’utilisateur u3 du groupe groupe2. Que se passe-t-il ? Expliquez.**

```
root@ubuntu-server:/home# gpasswd -d u3 groupe2
Removing user u3 from group groupe2
```

Ne fonctionne pas car que groupe2 est l'unique groupe de u3

**Modifiez le compte de u4 de sorte que :**

— il expire au 1er juin 2019

— il faut changer de mot de passe avant 90 jours

— il faut attendre 5 jours pour modifier un mot de passe

— l’utilisateur est averti 14 jours avant l’expiration de son mot de passe

— le compte sera bloqué 30 jours après expiration du mot de passe


```
sudo chage -d 06/01/20 -M 90 -m 5 -W 14 --expiredate 09/30/20 u4
```

**Quel est l’interpréteur de commandes (Shell) de l’utilisateur root ?**


```
thomas@ubuntu-server:~$ grep root /etc/passwd
root:x:0:0:root:/root:/bin/bash
```

Ou bien
```
sudo echo $SHELL
```

C'est /bin/bash

**à quoi correspond l’utilisateur nobody ?**

```
thomas@ubuntu-server:~$ grep nobody /etc/passwd
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
```

Nobody est un utilisateur qui n'a aucun droit sous linux, les droits minimun.
Il a donc que les droits défini dans other des fichiers.

**Par défaut, combien de temps la commande sudo conserve-t-elle votre mot de passe en mémoire ?
Quelle commande permet de forcer sudo à oublier votre mot de passe ?**

Par défault c'est 15 minutes j'ai vu avec man sudo

La commande est sudo --reset-timestamp et il demande à chaque fois le mot de passe

## Exercice 2. Gestion des permissions

**Dans votre $HOME, créez un dossier test, et dans ce dossier un fichier fichier contenant quelques
lignes de texte. Quels sont les droits sur test et fichier ?**

```
thomas@ubuntu-server:~$ ls -l test
total 4
-rw-rw-r-- 1 thomas thomas 25 oct.   2 09:08 fichier
thomas@ubuntu-server:~$ ls -l test/fichier
-rw-rw-r-- 1 thomas thomas 25 oct.   2 09:08 test/fichier
```

Les droits sont -rw-rw-r-- pour test et fichier

**Retirez tous les droits sur ce fichier (même pour vous), puis essayez de le modifier et de l’afficher en
tant que root. Conclusion ?**

```
thomas@ubuntu-server:~/test$ chmod ugo-rwx fichier
thomas@ubuntu-server:~/test$ ls -l
total 4
---------- 1 thomas thomas 25 oct.   2 09:08 fichier
thomas@ubuntu-server:~/test$ echo texte > fichier
-bash: fichier: Permission denied
thomas@ubuntu-server:~/test$ cat fichier
cat: fichier: Permission denied
thomas@ubuntu-server:~/test$ sudo echo texte > fichier
-bash: fichier: Permission denied
thomas@ubuntu-server:~/test$ sudo su
[sudo] password for thomas:
root@ubuntu-server:/home/thomas/test# echo texte > fichier
root@ubuntu-server:/home/thomas/test# cat fichier
texte
```

Après avoir retirer tout les droits sur le fichier root a quand même un accès donc root a tout les droits peut importe les permissions définies sur les fichiers (par contre je n'ai pas d'accès avec le sudo, pourquoi ? Je ne sais pas (peut être car le $USER en sudo est "thomas" et pas root ?))

**Redonnez vous les droits en écriture et exécution sur fichier puis exécutez la commande echo "echo
Hello" > fichier. On a vu lors des TP précédents que cette commande remplace le contenu d’un
fichier s’il existe déjà. Que peut-on dire au sujet des droits ?**

On peut dire que la commande fonctionne car elle a juste besoin d'écrire pour écrire la ligne sur le fichier.
Les permissions en lecture sont donc inutiles dans ce cas.

**Essayez d’exécuter le fichier. Est-ce que cela fonctionne ? Et avec sudo ? Expliquez.**

```
thomas@ubuntu-server:~/test$ ./fichier
bash: ./fichier: Permission denied
thomas@ubuntu-server:~/test$ sudo ./fichier
Hello
```

On ne peut pas executer le fichier car on ne peut pas le lire avec l'utilisateur thomas mais root lui peut lire et executer donc il affiche "Hello"

**Placez-vous dans le répertoire test, et retirez-vous le droit en lecture pour ce répertoire. Listez le
contenu du répertoire, puis exécutez ou affichez le contenu du fichier fichier. Qu’en déduisez-vous ?
Rétablissez le droit en lecture sur test**

```
thomas@ubuntu-server:~/test$ chmod u-r ../test/
thomas@ubuntu-server:~/test$ ls
ls: cannot open directory '.': Permission denied
thomas@ubuntu-server:~/test$ cat fichier
cat: fichier: Permission denied
thomas@ubuntu-server:~/test$ ./fichier
bash: ./fichier: Permission denied
```

On ne peut pas lister le contenu du répertoire car on a retirer le droit de lecture

On ne peut toujours pas afficher et executer fichier car il n'a toujours pas les droits de lecture sinon avec droits de lecture ça fonctionnerait

**Créez dans test un fichier nouveau ainsi qu’un répertoire sstest. Retirez au fichier nouveau et au
répertoire test le droit en écriture. Tentez de modifier le fichier nouveau. Rétablissez ensuite le droit
en écriture au répertoire test. Tentez de modifier le fichier nouveau, puis de le supprimer. Que pouvez-
vous déduire de toutes ces manipulations ?**

```
thomas@ubuntu-server:~/test$ ls -l
total 8
--wx------ 1 thomas thomas   11 oct.   2 09:26 fichier
-r--rw-r-- 1 thomas thomas    0 oct.   2 09:41 nouveau
drwxrwxr-x 2 thomas thomas 4096 oct.   2 09:44 sstest
thomas@ubuntu-server:~/test$ echo test > nouveau
-bash: nouveau: Permission denied
thomas@ubuntu-server:~/test$ chmod u+w ../test/
thomas@ubuntu-server:~/test$ echo test > nouveau
-bash: nouveau: Permission denied
thomas@ubuntu-server:~/test$ rm nouveau
rm: remove write-protected regular empty file 'nouveau'? yes
thomas@ubuntu-server:~/test$ ls
fichier  sstest
```

On en déduit que les droits de fichier sont uniquement lier au fichier et du dossier uniquement au dossier.
Ils ne sont pas liés.

**Positionnez vous dans votre répertoire personnel, puis retirez le droit en exécution du répertoire test.
Tentez de créer, supprimer, ou modifier un fichier dans le répertoire test, de vous y déplacer, d’en
lister le contenu, etc...Qu’en déduisez vous quant au sens du droit en exécution pour les répertoires ?**

```
thomas@ubuntu-server:~$ chmod u-x test
thomas@ubuntu-server:~$ touch test/fichiertest
touch: cannot touch 'test/fichiertest': Permission denied
thomas@ubuntu-server:~$ rm test/fichier
rm: cannot remove 'test/fichier': Permission denied
thomas@ubuntu-server:~$ nano test/fichier
thomas@ubuntu-server:~$ echo test >  test/fichier
-bash: test/fichier: Permission denied
thomas@ubuntu-server:~$ cd test/
-bash: cd: test/: Permission denied
thomas@ubuntu-server:~$ ls test/
ls: cannot access 'test/sstest': Permission denied
ls: cannot access 'test/fichier': Permission denied
fichier  sstest
thomas@ubuntu-server:~$ ls -l test/
ls: cannot access 'test/sstest': Permission denied
ls: cannot access 'test/fichier': Permission denied
total 0
-????????? ? ? ? ?              ? fichier
d????????? ? ? ? ?              ? sstest
```

On en déduit que si on ne peut pas executer un dossier, on a aucun droit sur son contenu.

**Rétablissez le droit en exécution du répertoire test. Positionnez vous dans ce répertoire et retirez lui
à nouveau le droit d’exécution. Essayez de créer, supprimer et modifier un fichier dans le répertoire
test, de vous déplacer dans ssrep, de lister son contenu. Qu’en concluez-vous quant à l’influence des
droits que l’on possède sur le répertoire courant ? Peut-on retourner dans le répertoire parent avec ”cd
..” ? Pouvez-vous donner une explication ?**

```
thomas@ubuntu-server:~$ chmod u+x test/
thomas@ubuntu-server:~$ cd test/
thomas@ubuntu-server:~/test$ chmod u-x ../test
thomas@ubuntu-server:~/test$ touch unfichier
touch: cannot touch 'unfichier': Permission denied
thomas@ubuntu-server:~/test$ rm fichier
rm: cannot remove 'fichier': Permission denied
thomas@ubuntu-server:~/test$ echo test >> fichier
-bash: fichier: Permission denied
thomas@ubuntu-server:~/test$ cd ssrep
-bash: cd: ssrep: Permission denied
thomas@ubuntu-server:~/test$ ls -la ssrep
ls: cannot access 'ssrep': Permission denied
thomas@ubuntu-server:~/test$ cd ..
```

Les droits du répertoire courants sont ("récursif") sur les répertoires enfants

Oui on peut faire cd ..

L'explication est que si on peut pas se placer dans un répertoire, on ne peut pas le traverser et donc aller dans les prochains dossiers
