Lab 1 : Git intro
=================

Travail fait sur Fedora KDE. Dans ce lab j'installe et je configure Git, je crée un dépôt local, je fais mes premiers commits et je corrige des erreurs avec checkout, amend et reset.

Installation
------------

Git était déjà installé sur ma machine, je vérifie seulement la version.

```bash
git --version
```

```
git version 2.55.0
```

Configuration
-------------

J'active les couleurs, je renseigne mon nom et mon adresse e-mail, puis je crée les quatre alias de l'énoncé avec git config --global. Je ne modifie pas le fichier .gitconfig à la main, le résultat est le même.

```bash
git config --global color.diff auto
git config --global color.status auto
git config --global color.branch auto
git config --global user.name "Hedi"
git config --global user.email "adresse@exemple.com"
git config --global alias.ci commit
git config --global alias.co checkout
git config --global alias.st status
git config --global alias.br branch
git config --list
```

Extrait de la sortie de git config --list :

```
color.diff=auto
color.status=auto
color.branch=auto
alias.ci=commit
alias.co=checkout
alias.st=status
alias.br=branch
```

Création d'un dépôt local
-------------------------

```bash
mkdir -p ~/Desktop/LabsGit/FirstProject
cd ~/Desktop/LabsGit/FirstProject
git init
```

```
Initialized empty Git repository in /home/Hedi_AM/Desktop/LabsGit/FirstProject/.git/
```

Clonage d'un dépôt existant
---------------------------

```bash
cd ~/Desktop/LabsGit
git clone https://git.savannah.gnu.org/git/hello.git Gnu-Hello
```

Le dépôt Gnu-Hello apparaît à côté de FirstProject.

Premiers fichiers et indexation
-------------------------------

Dans FirstProject, git status indique qu'il n'y a encore aucun commit. Je crée ensuite deux fichiers, avec la faute "Prject" dans le premier comme demandé.

```bash
git status
touch file1.txt file2.txt
echo "My first Git Prject" > file1.txt
echo "Hello World" > file2.txt
git status
```

Les deux fichiers sont dans les fichiers non suivis. Je n'ajoute que file1.txt à la zone d'index.

```bash
git add file1.txt
```

Je corrige ensuite la faute et je regarde l'état.

```bash
echo "My first Git Project" > file1.txt
git status
git diff file1.txt
```

git status affiche file1.txt deux fois : dans les changements à valider (version avec la faute) et dans les changements non indexés (version corrigée). git diff montre la différence entre les deux :

```
diff --git a/file1.txt b/file1.txt
index ff5c5ba..57ecbbd 100644
--- a/file1.txt
+++ b/file1.txt
@@ -1 +1 @@
-My first Git Prject
+My first Git Project
```

Fichier privé ajouté par erreur
-------------------------------

Je crée private.txt et je fais git add -A, ce qui l'ajoute à l'index avec le reste. Je le retire de l'index sans le supprimer du disque, puis je l'ajoute à .gitignore.

```bash
touch private.txt
git add -A
git status
git rm --cached private.txt
echo private.txt >> .gitignore
git add -A
git status
```

Après le dernier git add -A, l'index contient .gitignore, file1.txt et file2.txt. private.txt n'apparaît plus.

Récupérer une version indexée
-----------------------------

Je vide file1.txt par erreur, puis je le récupère depuis la zone d'index.

```bash
echo '' > file1.txt
git status
git checkout file1.txt
cat file1.txt
```

```
Updated 1 path from the index
My first Git Project
```

Premier commit
--------------

Un git commit sans message est refusé, puis je commite avec -m.

```bash
git commit
git commit -m "First commit"
git status
```

```
Aborting commit due to empty commit message.
[master (root-commit) 2001404] First commit
 3 files changed, 3 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 file1.txt
 create mode 100644 file2.txt
On branch master
nothing to commit, working tree clean
```

Modifier le dernier message de commit
-------------------------------------

J'ajoute une ligne dans file2.txt, je commite avec le même message que le premier commit, puis je change ce message avec --amend.

```bash
echo 'Ligne erreur' >> file2.txt
git add file2.txt
git commit -m "First commit"
git commit --amend -m "j'ai ajouté une ligne erreur"
git log
```

Avant le amend, le nouveau commit s'appelle f500d53 et porte le message "First commit". Après, il s'appelle 1ff7c8b et son message est "j'ai ajouté une ligne erreur". Le commit 2001404 en dessous n'a pas changé. L'identifiant change parce que amend remplace le commit.

Annuler un commit avec reset
----------------------------

Je corrige la ligne, puis je reviens au premier commit en gardant les modifications dans l'index.

```bash
sed -i 's/Ligne erreur/Ligne corrigée/' file2.txt
git reset --soft 2001404
git log
git status
```

Après le reset, git log ne montre plus que le commit 2001404. git status affiche file2.txt à la fois dans l'index (ancienne modification) et dans les changements non indexés (ma correction). Je réindexe et je commite.

```bash
git add file2.txt
git commit -m "Ajout ligne corrigée"
git show HEAD
```

```
commit 5a6d069284f9629d6f31fec48f6522926dcd8459 (HEAD -> master)
Author: Hedi <adresse@exemple.com>
Date:   Sun Oct 11 13:22:14 2026 +0100

    Ajout ligne corrigée

diff --git a/file2.txt b/file2.txt
index 557db03..0f2276f 100644
--- a/file2.txt
+++ b/file2.txt
@@ -1 +1,2 @@
 Hello World
+Ligne corrigée
```

Remarques
---------

Ma version de Git est plus récente que celle de l'énoncé. Les messages d'aide de git status proposent git restore à la place de git checkout --, mais git checkout file1.txt fonctionne toujours. Les identifiants de commit sont différents de ceux de l'énoncé, ce qui est normal.
