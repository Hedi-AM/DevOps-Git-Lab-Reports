Lab 2 : Git branch
==================

Dans ce lab je gère un petit site web avec Git. Je travaille avec plusieurs branches, je les fusionne, je pose un tag et je résous un conflit de fusion.

Préparation
-----------

Je crée l'alias lgone demandé dans l'énoncé pour afficher les logs sur une seule ligne.

```bash
git config --global alias.lgone 'log --oneline --decorate'
```

Je n'ai pas modifié le prompt du terminal (la variable PS1 dans .bashrc), car l'énoncé le propose seulement en option. Pour voir ma branche courante j'utilise git branch.

Dépôt du site
-------------

Le dossier site-prod contient le site en ligne (branche master) et site-dev les nouveautés en cours.

```bash
mkdir -p ~/Desktop/website/{site-prod,site-dev}
cd ~/Desktop/website
git init
echo '<h1>Hello World!<h1\>' > site-prod/index.html
git add -A
git status
git commit -q -m "The beginnings of my web site."
git lgone
git branch
```

Le dépôt est créé sur la branche master avec un seul commit, 0f9f372.

Branche new-features
--------------------

Je crée la branche new-features, je m'y place et j'ajoute deux pages, chacune dans son commit.

```bash
git branch new-features
git checkout new-features
echo 'Contact : adresse@exemple.com' > site-dev/contact.html
git add -A
git commit -m "Added contact.html file"
echo 'Help : God bless you' > site-dev/help.html
git add -A
git commit -m "Added help.html file"
git lgone
```

git lgone montre les deux nouveaux commits sur new-features, et master reste sur le premier commit.

```bash
git checkout master
git lgone
ls site-dev
```

Sur master, git lgone ne montre que "The beginnings of my web site." et le dossier site-dev a disparu, parce que ses fichiers n'existent que sur new-features.

Branche php-features
--------------------

Je crée la branche php-features depuis master. Comme site-dev n'existe pas sur master, je le recrée avant d'y mettre index.php.

```bash
git checkout -b php-features
mkdir -p site-dev
nano site-dev/index.php
git add -A
git commit -m "Added index.php"
git lgone
```

Le fichier index.php contient une page HTML qui affiche "Hello World!" avec un bloc PHP (echo "Hello World!";).

Modifications sur master
------------------------

De retour sur master, j'ajoute un README.md et je remplace le contenu de site-prod/index.html par un squelette HTML complet.

```bash
git checkout master
echo 'My First HTML Site' > README.md
nano site-prod/index.html
git add -A
git status
git commit -m "Added html skeleton to index.html and REDME.md"
git lgone
```

git status montre README.md comme nouveau fichier et site-prod/index.html comme modifié. Le "REDME" dans le message de commit vient de l'énoncé.

Fusion de new-features
----------------------

```bash
git merge new-features
git lgone
git lgone --graph
git branch -d new-features
git branch
```

La fusion crée un commit "Merge branch 'new-features'", avec les deux fichiers contact.html et help.html ajoutés à master. Comme tous ses commits sont dans master, je supprime la branche new-features. Il reste master et php-features.

Tag
---

```bash
git tag v0.1
git lgone
```

Le tag v0.1 est posé sur le commit de fusion de new-features.

Conflit de fusion
-----------------

Sur php-features j'ajoute un README.md, alors que master en a déjà un avec un autre contenu.

```bash
git checkout php-features
echo 'My First PHP Site' > README.md
git add -A
git commit -m "Added README.md file"
git checkout master
git log --oneline --decorate --graph --all
git merge php-features
```

La fusion échoue :

```
Auto-merging README.md
CONFLICT (add/add): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

git status indique README.md comme "both added". Le fichier contient les marques de conflit :

```
<<<<<<< HEAD
My First HTML Site
=======
My First PHP Site
>>>>>>> php-features
```

Je résous le conflit à la main avec nano : je supprime les marques et les deux lignes, et j'écris une seule ligne.

```
My First HTML-PHP Site
```

Ensuite j'indique à Git que le conflit est réglé et je termine la fusion.

```bash
git add -A
git status
git commit
git log --oneline --decorate --graph
```

git status dit alors que tous les conflits sont réglés mais que la fusion n'est pas terminée. git commit utilise le message proposé, "Merge branch 'php-features'".

Historique final
----------------

```
*   3a37f20 (HEAD -> master) Merge branch 'php-features'
|\
| * 78e4a8b (php-features) Added README.md file
| * f1e80f5 Added index.php
* |   e74777c (tag: v0.1) Merge branch 'new-features'
|\ \
| * | 9567861 Added help.html file
| * | 9f1df80 Added contact.html file
| |/
* / a13fdae Added html skeleton to index.html and REDME.md
|/
* 0f9f372 The beginnings of my web site.
```

Le README.md final contient "My First HTML-PHP Site" et git status indique que l'arbre de travail est propre.

Remarques
---------

Le message de la fusion de new-features est celui proposé par Git, "Merge branch 'new-features'", et non celui de l'énoncé. Je n'ai pas utilisé gitk pour afficher le graphe, git lgone --graph donne la même information dans le terminal.
