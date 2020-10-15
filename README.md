create a new repository on the command line
echo "# -" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/3218870799/-Note.git
git push -u origin main