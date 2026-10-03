
Nom : Hedi Ben Abdelmoumen

 Commandes executees
git config --global user.name "Hedi"
git config --global user.email "monemail@gmail.com"
mkdir -p ~/Desktop/DevOps-Labs/Lab1
cd ~/Desktop/DevOps-Labs/Lab1
git init
echo "# Lab 1 - Git Basics" > README.md
git add README.md
git commit -m "Initial commit: Add README.md"
touch secret.env app.log
echo "*.log" > .gitignore
echo "*.env" >> .gitignore
git add .gitignore
git commit -m "Add .gitignore to exclude log and env files"
