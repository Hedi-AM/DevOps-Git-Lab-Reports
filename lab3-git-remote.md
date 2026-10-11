Lab 3 : Git remote
==================

Dans ce lab je travaille avec des dépôts distants sur GitHub : création d'un dépôt, clone, fetch et pull, push, branche distante, puis un fork avec une pull request faite depuis un deuxième compte.

Dépôt GitHub
------------

Depuis mon compte Hedi-AM, je crée un dépôt public test-demo avec la description "Repo GitHub Demo Test" et l'option "Initialize this repository with a README" cochée. Je modifie ensuite le README.md depuis l'interface web pour ajouter la ligne "New line from GitHub WebGUI", et je commite directement sur la branche principale avec le message "Add line to README.md from WebGUI".

Clone et dépôts distants
------------------------

```bash
cd ~/Desktop/LabsGit
git clone https://github.com/Hedi-AM/test-demo.git
cd test-demo
git remote
git remote -v
git remote show origin
```

git remote affiche origin, et git remote -v donne l'URL du dépôt pour fetch et pour push.

Fetch et pull
-------------

Autre façon d'obtenir une copie locale : je crée un dépôt vide, j'ajoute le distant, je récupère puis je tire.

```bash
cd ~/Desktop/LabsGit
mkdir fetch-test
cd fetch-test
git init
git remote add origin https://github.com/Hedi-AM/test-demo.git
git remote -v
git fetch origin
git remote show origin
git pull origin main
ls
git lgone
```

Après le pull, le dossier contient README.md et git lgone montre l'historique du dépôt distant.

Push
----

Dans test-demo, j'ajoute une ligne au README.md, je commite et je pousse.

```bash
cd ~/Desktop/LabsGit/test-demo
echo "New line from local git repo." >> README.md
git status
git add -A
git commit -m "Add new line from local repo to README.md"
git lgone --graph
git push
```

Je vérifie sur GitHub que le commit "Add new line from local repo to README.md" est bien présent.

Branche distante
----------------

Je crée la branche feature-1 en local, j'y ajoute un fichier et je la pousse sur GitHub.

```bash
git branch feature-1
git checkout feature-1
echo "Feature-1 content" > feature1.txt
git add -A
git commit -m "Add Branch Feature-1 and file feature1.txt"
git checkout main
git push origin feature-1
```

Le dépôt GitHub affiche ensuite deux branches, main et feature-1.

Pull request
------------

Je commence par introduire une erreur dans le README.md sur la branche principale, avec mon compte principal.

```bash
git checkout main
echo "ERRROR" >> README.md
git add -A
git commit -m "Add Error"
git push origin main
```

Fork. Avec mon deuxième compte, Hedi0AM, je me rends sur le dépôt Hedi-AM/test-demo et je clique sur Fork. Je ne fork que la branche main.

Clone. Au lieu de créer un deuxième utilisateur Linux comme dans l'énoncé, je clone le fork dans un dossier séparé, test-demo-fix, et je règle l'identité Git du deuxième compte seulement dans ce dossier (sans --global).

```bash
cd ~/Desktop/LabsGit
git clone https://github.com/Hedi0AM/test-demo.git test-demo-fix
cd test-demo-fix
git config user.name "Hedi0AM"
git config user.email "adresse@exemple.com"
```

Nouvelle branche et correction.

```bash
git branch fix-typo
git checkout fix-typo
cat README.md
sed -i 's/^ERRROR/Fixed ERRROR/' README.md
cat README.md
git add -A
git commit -m "Fix error in README.md"
git log
```

Le commit 2d012e7 est bien signé Hedi0AM et se trouve au-dessus de mon commit "Add Error".

Push de la branche. Mon outil gh est connecté au compte principal, donc je me suis authentifié pour ce push avec un jeton d'accès personnel du deuxième compte, créé pour l'occasion.

```bash
git push https://Hedi0AM@github.com/Hedi0AM/test-demo.git fix-typo
```

```
remote: Create a pull request for 'fix-typo' on GitHub by visiting:
remote:      https://github.com/Hedi0AM/test-demo/pull/new/fix-typo
To https://github.com/Hedi0AM/test-demo.git
 * [new branch]      fix-typo -> fix-typo
```

Mise à jour du fork. J'ajoute le dépôt d'origine comme remote upstream, je le récupère et je fusionne sa branche principale dans la mienne.

```bash
git remote -v
git remote add upstream https://github.com/Hedi-AM/test-demo.git
git remote -v
git fetch upstream
git checkout main
git merge upstream/main
```

Création et validation de la pull request. Depuis Hedi0AM, j'ouvre une pull request de fix-typo vers Hedi-AM:main avec le titre "Fix error in README.md" et le commentaire "Fixed Error in file README.md". Avec le compte Hedi-AM, je trouve la pull request dans l'onglet Pull requests, je regarde l'onglet Files changed (la ligne ERRROR est remplacée par Fixed ERRROR), puis je fusionne avec Merge pull request et Confirm merge.

Récupérer les changements
-------------------------

La fusion a eu lieu sur GitHub, donc je la récupère dans mon clone local.

```bash
cd ~/Desktop/LabsGit/test-demo
git pull origin main
cat README.md
```

Le README.md se termine par "Fixed ERRROR".

État final du dépôt
-------------------

```
*   d3e4fc1 (HEAD -> main, origin/main, origin/HEAD) Merge pull request #1 from Hedi0AM/fix-typo
|\
| * 2d012e7 Fix error in README.md
|/
* 08f6619 Add Error
| * 2cac538 (origin/feature-1, feature-1) Add Branch Feature-1 and file feature1.txt
|/
* 237a210 Add new line from local repo to README.md
* 7efa551 Add line to README.md from WebGUI
* 8c5a705 Update README.md
* 6594640 Initial commit
```

Remarques
---------

La branche principale s'appelle main sur mon dépôt GitHub, alors que l'énoncé écrit master. J'ai donc utilisé main dans les commandes git pull et git push. Je n'ai pas lancé les commandes git config credential.helper de l'énoncé, parce que gh gère déjà l'authentification pour mon compte principal. Pour le deuxième utilisateur, j'ai utilisé un dossier séparé avec une identité locale plutôt qu'un compte Linux supplémentaire.
