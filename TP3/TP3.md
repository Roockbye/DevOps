## 2. Initialisez un projet React vierge

On install npx :
```
rocky@pacman:~/Documents/DevOps$ npx create-react-app app
bash: npx : commande introuvable
rocky@pacman:~/Documents/DevOps$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 16555  100 16555    0     0  24322      0 --:--:-- --:--:-- --:--:-- 24309
=> Downloading nvm from git to '/home/rocky/.nvm'
=> Clonage dans '/home/rocky/.nvm'...
remote: Enumerating objects: 382, done.
remote: Counting objects: 100% (382/382), done.
remote: Compressing objects: 100% (325/325), done.
remote: Total 382 (delta 43), reused 180 (delta 29), pack-reused 0 (from 0)
Réception d'objets: 100% (382/382), 385.06 Kio | 237.00 Kio/s, fait.
Résolution des deltas: 100% (43/43), fait.
* (HEAD détachée sur FETCH_HEAD)
  master
=> Compressing and cleaning up git repository

=> Appending nvm source string to /home/rocky/.bashrc
=> Appending bash_completion source string to /home/rocky/.bashrc
=> Close and reopen your terminal to start using nvm or run the following to use it now:

export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
rocky@pacman:~/Documents/DevOps$ source ~/.bashrc
rocky@pacman:~/Documents/DevOps$ nvm install 18
Downloading and installing node v18.20.8...
Downloading https://nodejs.org/dist/v18.20.8/node-v18.20.8-linux-x64.tar.xz...
########################################################################################################################################################################## 100.0%
Computing checksum with sha256sum
Checksums matched!
Now using node v18.20.8 (npm v10.8.2)
Creating default alias: default -> 18 (-> v18.20.8)
rocky@pacman:~/Documents/DevOps$ nvm use 18
Now using node v18.20.8 (npm v10.8.2)
rocky@pacman:~/Documents/DevOps$ node -v
v18.20.8
```
On crée notre projet vierge :
```
rocky@pacman:~/Documents/DevOps$ npx create-react-app app
Need to install the following packages:
create-react-app@5.1.0
Ok to proceed? (y) y

npm warn deprecated inflight@1.0.6: This module is not supported, and leaks memory. Do not use it. Check out lru-cache if you want a good and tested way to coalesce async requests by a key value, which is much more comprehensive and powerful.
npm warn deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported
npm warn deprecated uid-number@0.0.6: This package is no longer supported.
npm warn deprecated fstream-ignore@1.0.5: This package is no longer supported.
npm warn deprecated rimraf@2.7.1: Rimraf versions prior to v4 are no longer supported
npm warn deprecated fstream@1.0.12: This package is no longer supported.
npm warn deprecated tar@2.2.2: This version of tar is no longer supported, and will not receive security updates. Please upgrade asap.
create-react-app is deprecated.

You can find a list of up-to-date React frameworks on react.dev
For more info see:https://react.dev/link/cra

This error message will only be shown once per install.

Creating a new React app in /home/rocky/Documents/DevOps/app.

Installing packages. This might take a couple of minutes.
Installing react, react-dom, and react-scripts with cra-template...


added 1324 packages in 1m

269 packages are looking for funding
  run `npm fund` for details

Installing template dependencies using npm...

added 18 packages, and changed 1 package in 12s

269 packages are looking for funding
  run `npm fund` for details
Removing template package using npm...


removed 1 package, and audited 1342 packages in 4s

269 packages are looking for funding
  run `npm fund` for details

8 vulnerabilities (2 moderate, 6 high)

To address all issues (including breaking changes), run:
  npm audit fix --force

Run `npm audit` for details.

Success! Created app at /home/rocky/Documents/DevOps/app
Inside that directory, you can run several commands:

  npm start
    Starts the development server.

  npm run build
    Bundles the app into static files for production.

  npm test
    Starts the test runner.

  npm run eject
    Removes this tool and copies build dependencies, configuration files
    and scripts into the app directory. If you do this, you can’t go back!

We suggest that you begin by typing:

  cd app
  npm start

Happy hacking!
npm notice
npm notice New major version of npm available! 10.8.2 -> 11.4.1
npm notice Changelog: https://github.com/npm/cli/releases/tag/v11.4.1
npm notice To update run: npm install -g npm@11.4.1
npm notice
```
L'installation a bien eu lieu malgré les avertissements de dépréciation...

## 3. Vérifiez que l'application fonctionne correctement en local

```
cd app
```
```
npm start
```
Nous pouvons accéder à l'application depuis : http://localhost:3000/

[project react](../captures/react.png)

## 4. Créez un fichier Dockerfile à la racine du projet. Celui-ci doit se diviser en deux grandes parties :

### a. Étape 1 : Construction de l'application React FROM node:18-alpine as build

### b. Étape 2 : Serveur web pour l'application FROM nginx:alpine

## 5. Instanciez l’image créé précédemment afin d’observer le même résultat que lors de la question 3