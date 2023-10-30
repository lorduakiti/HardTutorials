```
apt update
apt install git

git --version

git config --global user.name "S???"
git config --global user.email "???@???.com"

nano ~/.gitconfig
git config --list

git add *
git commit
git commit -m "descrição do commit.."
git push

git pull origin master

git rm package-lock.json
git rm --cached package-lock.json
git update-index --assume-unchanged package-lock.json
git update-index --no-assume-unchanged package-lock.json
```