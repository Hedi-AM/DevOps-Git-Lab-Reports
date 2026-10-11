Lab 4 : Installation et exploitation de Git et Forgejo
======================================================

Dans ce lab je déploie Forgejo dans un conteneur Docker sur une machine virtuelle Linux, puis j'utilise Git avec un dépôt distant hébergé sur ce Forgejo.

Git
---

Git est installé et configuré depuis le Lab 1 (git --version affiche 2.55.0, nom et e-mail déjà définis avec git config --global).

Projet local et commits
-----------------------

```bash
cd ~/Desktop/LabsGit
mkdir ProjetForgejo
cd ProjetForgejo
git init
touch file1.txt file2.txt
git add .
git commit -m "Initialisation du projet"
```

Pour arriver aux trois commits demandés, j'en ajoute deux autres :

```bash
echo "Premier contenu" > file1.txt
git add file1.txt
git commit -m "Ajout du contenu dans file1.txt"
echo "Deuxieme contenu" > file2.txt
git add file2.txt
git commit -m "Ajout du contenu dans file2.txt"
git branch -M main
```

J'ai renommé la branche en main avec git branch -M main, parce que git init crée master et que l'énoncé pousse vers main.

Machine virtuelle
-----------------

J'utilise une VM Linux Alpine 3.24.2 créée avec mon gestionnaire de machines virtuelles. Voici ses caractéristiques :

```
Adresse IP : 192.168.122.180
Processeurs : 2
Mémoire : 965 Mo
Docker : version 26.1.5
```

Docker était déjà installé et fonctionnait sur cette VM, donc je n'ai pas eu besoin de lancer apt install docker.io.

Déploiement de Forgejo
----------------------

Je crée le volume de données :

```bash
docker volume create forgejo_data
```

L'énoncé utilise l'image codeberg.org/forgejo/forgejo:latest. Ce tag n'existe pas, docker répond "not found". Forgejo publie ses images avec des numéros de version, j'ai donc utilisé le tag 16.

La VM n'avait pas accès à Internet : ping 8.8.8.8 sans réponse et le téléchargement de l'image depuis codeberg.org s'est terminé par un timeout. J'ai donc récupéré l'image sur mon poste, où elle était déjà téléchargée, et je l'ai envoyée dans la VM par SSH :

```bash
docker save codeberg.org/forgejo/forgejo:16 | ssh root@192.168.122.180 docker load
```

Ensuite, dans la VM, je lance le conteneur :

```bash
docker run -d --name forgejo -p 3000:3000 -p 2222:22 -v forgejo_data:/data codeberg.org/forgejo/forgejo:16
docker ps
```

```
CONTAINER ID   IMAGE                             COMMAND                  CREATED         STATUS         PORTS                                                                    NAMES
bee7e70eebd4   codeberg.org/forgejo/forgejo:16   "/usr/bin/entrypoint…"   5 seconds ago   Up 4 seconds   0.0.0.0:3000->3000/tcp, :::3000->3000/tcp, 0.0.0.0:2222->22/tcp, :::2222->22/tcp   forgejo
```

Le conteneur forgejo est démarré et expose les ports 3000 (web) et 2222 (SSH).

Accès à Forgejo
---------------

J'ouvre http://192.168.122.180:3000 dans le navigateur de mon poste et la page de configuration initiale s'affiche. Je garde les valeurs par défaut, sauf pour deux champs que je remplace par l'adresse de la VM à la place de localhost :

```
Type de base de données : SQLite3
Domaine du serveur : 192.168.122.180
URL de base de Forgejo : http://192.168.122.180:3000/
```

Je crée aussi le compte administrateur hedi dans la section des paramètres du compte administrateur, puis je clique sur Installer Forgejo. Je suis ensuite connecté sur le tableau de bord.

Création du dépôt
-----------------

Depuis l'interface web, je crée un dépôt nommé tp-forgejo, sans README ni autre fichier initial, pour qu'il soit vide. Son adresse est http://192.168.122.180:3000/hedi/tp-forgejo.

Liaison et publication
----------------------

Dans ProjetForgejo, j'enregistre le dépôt Forgejo comme remote origin et je pousse la branche main.

```bash
cd ~/Desktop/LabsGit/ProjetForgejo
git remote set-url origin http://192.168.122.180:3000/hedi/tp-forgejo.git
git remote -v
git push -u origin main
```

J'avais d'abord enregistré origin avec git remote add, puis je l'ai modifié avec git remote set-url pour qu'il pointe vers la VM. Le push demande l'identifiant et le mot de passe du compte hedi de Forgejo.

```
To http://192.168.122.180:3000/hedi/tp-forgejo.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

Sur la page du dépôt, je retrouve file1.txt, file2.txt et les trois commits.

Clonage
-------

Je clone le dépôt dans un autre dossier pour ne pas le mélanger avec ProjetForgejo.

```bash
cd ~/Desktop/LabsGit
git clone http://192.168.122.180:3000/hedi/tp-forgejo.git
cd tp-forgejo
ls
git lgone
```

Le dossier tp-forgejo contient file1.txt et file2.txt, et git lgone montre les trois commits.

Synchronisation
---------------

```bash
git pull
git push
```

```
Already up to date.
Everything up-to-date
```

Rien n'a changé depuis le clone, donc il n'y a rien à récupérer ni à envoyer.

Remarques
---------

L'énoncé demande une VM Ubuntu avec au moins 2 vCPU et 4 Go de RAM. J'ai utilisé une VM Alpine Linux avec 2 vCPU et environ 1 Go de RAM, qui suffit pour Forgejo avec SQLite. Comme Alpine utilise apk et non apt, et que Docker y était déjà installé, la partie installation de Docker de l'énoncé n'a pas été refaite.
