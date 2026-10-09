
1. Configuration de Git
Configuration de l'identité globale et de la coloration du terminal :

git config --global user.name "Hedi"
git config --global user.email "monemail@gmail.com"
git config --global color.diff auto
git config --global color.status auto
git config --global color.branch auto

Vérification des paramètres :
git config --list

---

2. Création et Initialisation du Dépôt Local
mkdir -p ~/Desktop/DevOps-Labs/Lab1
cd ~/Desktop/DevOps-Labs/Lab1
git init

---

3. Gestion des Fichiers et Staging Area

Création des fichiers de test :
touch file1.txt file2.txt
echo "My first Git Project" > file1.txt
echo "Hello World" > file2.txt

Ajout au Staging Area et gestion d'un fichier privé :
touch private.txt
git add -A

Retrait du fichier sensible du Staging Area
git rm --cached private.txt

Exclusion via .gitignore
echo "private.txt" >> .gitignore
echo ".log" >> .gitignore
echo ".env" >> .gitignore

git add .gitignore
git commit -m "First commit: Initialisation du projet et .gitignore"

---

4. Restauration d'un Fichier Altéré
Simulation d'une erreur (vidage du fichier file1.txt) et restauration depuis l'index :

echo "" > file1.txt
git status                      Affiche le fichier modifié
git checkout -- file1.txt       Restauration du fichier
cat file1.txt                   Vérification du contenu restauré

---

5. Modification du Dernier Commit (--amend)
Ajout d'une modification et correction du dernier message de commit :

echo "Ligne erreur" >> file2.txt
git add file2.txt
git commit --amend -m "Correction : ajout de ligne dans file2"

---

6. Annulation d'un Commit (git reset)
Annulation douce du commit pour corriger une ligne :

Annulation du commit tout en gardant les fichiers modifiés dans le Staging Area
git reset --soft HEAD~1

Correction du contenu
sed -i 's/Ligne erreur/Ligne corrigée/' file2.txt
git add file2.txt
git commit -m "Ajout ligne corrigée"

Vérification de l'historique des commits :
git log --oneline
git show HEAD
