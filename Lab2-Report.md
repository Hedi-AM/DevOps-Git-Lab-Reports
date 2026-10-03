Nom : Hedi Ben Abdelmoumen
Commandes executees
mkdir -p ~/Desktop/DevOps-Labs/Lab2
cd ~/Desktop/DevOps-Labs/Lab2
git init
echo "# Project Setup" > README.md
git add README.md
git commit -m "Initial commit on main branch"

git checkout -b feature-login
echo "Feature: Login implementation" >> README.md
git add README.md
git commit -m "Add login feature description"

git checkout main
git merge feature-login

git checkout -b conflict-branch
echo "# Project Setup (Branch Version)" > README.md
git add README.md
git commit -m "Update title on conflict branch"

git checkout main
echo "# Project Setup (Main Version)" > README.md
git add README.md
git commit -m "Update title on main branch"

git merge conflict-branch

echo "# Project Setup (Final Version)" > README.md
echo "Feature: Login implementation" >> README.md
git add README.md
git commit -m "Resolve merge conflict in README.md"
