# GIT 사용방법


## …or create a new repository on the command line
```bash
echo "# setup" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:jkang-sys/setup.git
git push -u origin main
```
## …or push an existing repository from the command line
```bash
git remote add origin git@github.com:jkang-sys/setup.git
git branch -M main
git push -u origin main
```


