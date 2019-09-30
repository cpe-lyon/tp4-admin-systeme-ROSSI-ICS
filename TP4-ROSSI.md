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
