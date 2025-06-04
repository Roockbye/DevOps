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
```
```
rocky@pacman:~/Documents/DevOps$ source ~/.bashrc
```
```
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

(note: Penser à supprimer les anciens dockeurs qui tournent sur le port 3000)

Nous avons bien la page react qui se lance.

[project react](../captures/react.png)

## 4. Créez un fichier Dockerfile à la racine du projet. Celui-ci doit se diviser en deux grandes parties :

Voir fichier Dockerfile

### a. Étape 1 : Construction de l'application React FROM node:18-alpine as build

```
FROM node:18-alpine AS build
WORKDIR /app
COPY app/package*.json ./
RUN npm install
COPY app/ ./
RUN npm run build
```

### b. Étape 2 : Serveur web pour l'application FROM nginx:alpine

```
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

## 5. Instanciez l’image créé précédemment afin d’observer le même résultat que lors de la question 3

:!\ Attention : le dossier app doit se trouver dans le dossier TP3 car Docker n’autorise pas d’accéder à des fichiers situés en dehors du contexte de build.

```
rocky@pacman:~/Documents/DevOps/TP3$ docker build -t react-nginx-app .
[+] Building 42.1s (14/14) FINISHED                                                                                                                               docker:default
 => [internal] load build definition from Dockerfile                                                                                                                        0.0s
 => => transferring dockerfile: 270B                                                                                                                                        0.0s
 => [internal] load metadata for docker.io/library/node:18-alpine                                                                                                           0.9s
 => [internal] load metadata for docker.io/library/nginx:alpine                                                                                                             0.9s
 => [internal] load .dockerignore                                                                                                                                           0.0s
 => => transferring context: 2B                                                                                                                                             0.0s
 => [build 1/6] FROM docker.io/library/node:18-alpine@sha256:8d6421d663b4c28fd3ebc498332f249011d118945588d0a35cb9bc4b8ca09d9e                                               9.1s
 => => resolve docker.io/library/node:18-alpine@sha256:8d6421d663b4c28fd3ebc498332f249011d118945588d0a35cb9bc4b8ca09d9e                                                     0.0s
 => => sha256:8d6421d663b4c28fd3ebc498332f249011d118945588d0a35cb9bc4b8ca09d9e 7.67kB / 7.67kB                                                                              0.0s
 => => sha256:929b04d7c782f04f615cf785488fed452b6569f87c73ff666ad553a7554f0006 1.72kB / 1.72kB                                                                              0.0s
 => => sha256:ee77c6cd7c1886ecc802ad6cedef3a8ec1ea27d1fb96162bf03dd3710839b8da 6.18kB / 6.18kB                                                                              0.0s
 => => sha256:f18232174bc91741fdf3da96d85011092101a032a93a388b79e99e69c2d5c870 3.64MB / 3.64MB                                                                              3.6s
 => => sha256:dd71dde834b5c203d162902e6b8994cb2309ae049a0eabc4efea161b2b5a3d0e 40.01MB / 40.01MB                                                                            8.0s
 => => extracting sha256:f18232174bc91741fdf3da96d85011092101a032a93a388b79e99e69c2d5c870                                                                                  37.5s
 => => sha256:1e5a4c89cee5c0826c540ab06d4b6b491c96eda01837f430bd47f0d26702d6e3 1.26MB / 1.26MB                                                                              9.0s
 => => extracting sha256:dd71dde834b5c203d162902e6b8994cb2309ae049a0eabc4efea161b2b5a3d0e                                                                                   0.5s
 => => sha256:25ff2da83641908f65c3a74d80409d6b1b62ccfaab220b9ea70b80df5a2e0549 446B / 446B                                                                                  8.5s
 => => extracting sha256:1e5a4c89cee5c0826c540ab06d4b6b491c96eda01837f430bd47f0d26702d6e3                                                                                   0.0s
 => => extracting sha256:25ff2da83641908f65c3a74d80409d6b1b62ccfaab220b9ea70b80df5a2e0549                                                                                   0.0s
 => [internal] load build context                                                                                                                                           2.1s
 => => transferring context: 277.50MB                                                                                                                                       2.1s
 => [stage-1 1/2] FROM docker.io/library/nginx:alpine@sha256:65645c7bb6a0661892a8b03b89d0743208a18dd2f3f17a54ef4b76fb8e2f2a10                                              12.0s
 => => resolve docker.io/library/nginx:alpine@sha256:65645c7bb6a0661892a8b03b89d0743208a18dd2f3f17a54ef4b76fb8e2f2a10                                                       0.0s
 => => sha256:65645c7bb6a0661892a8b03b89d0743208a18dd2f3f17a54ef4b76fb8e2f2a10 10.33kB / 10.33kB                                                                            0.0s
 => => sha256:6769dc3a703c719c1d2756bda113659be28ae16cf0da58dd5fd823d6b9a050ea 10.79kB / 10.79kB                                                                            0.0s
 => => sha256:f18232174bc91741fdf3da96d85011092101a032a93a388b79e99e69c2d5c870 3.64MB / 3.64MB                                                                              3.6s
 => => sha256:61ca4f733c802afd9e05a32f0de0361b6d713b8b53292dc15fb093229f648674 1.79MB / 1.79MB                                                                              1.0s
 => => sha256:b464cfdf2a6319875aeb27359ec549790ce14d8214fcb16ef915e4530e5ed235 629B / 629B                                                                                  0.5s
 => => sha256:62223d644fa234c3a1cc785ee14242ec47a77364226f1c811d2f669f96dc2ac8 2.50kB / 2.50kB                                                                              0.0s
 => => sha256:d7e5070240863957ebb0b5a44a5729963c3462666baa2947d00628cb5f2d5773 955B / 955B                                                                                  0.9s
 => => sha256:81bd8ed7ec6789b0cb7f1b47ee731c522f6dba83201ec73cd6bca1350f582948 402B / 402B                                                                                  1.1s
 => => sha256:197eb75867ef4fcecd4724f17b0972ab0489436860a594a9445f8eaff8155053 1.21kB / 1.21kB                                                                              1.3s
 => => sha256:34a64644b756511a2e217f0508e11d1a572085d66cd6dc9a555a082ad49a3102 1.40kB / 1.40kB                                                                              1.4s
 => => sha256:39c2ddfd6010082a4a646e7ca44e95aca9bf3eaebc00f17f7ccc2954004f2a7d 15.52MB / 15.52MB                                                                           11.7s
 => => extracting sha256:f18232174bc91741fdf3da96d85011092101a032a93a388b79e99e69c2d5c870                                                                                   0.1s
 => => extracting sha256:61ca4f733c802afd9e05a32f0de0361b6d713b8b53292dc15fb093229f648674                                                                                   0.0s
 => => extracting sha256:b464cfdf2a6319875aeb27359ec549790ce14d8214fcb16ef915e4530e5ed235                                                                                   0.0s
 => => extracting sha256:d7e5070240863957ebb0b5a44a5729963c3462666baa2947d00628cb5f2d5773                                                                                   0.0s
 => => extracting sha256:81bd8ed7ec6789b0cb7f1b47ee731c522f6dba83201ec73cd6bca1350f582948                                                                                   0.0s
 => => extracting sha256:197eb75867ef4fcecd4724f17b0972ab0489436860a594a9445f8eaff8155053                                                                                   0.0s
 => => extracting sha256:34a64644b756511a2e217f0508e11d1a572085d66cd6dc9a555a082ad49a3102                                                                                   0.0s
 => => extracting sha256:39c2ddfd6010082a4a646e7ca44e95aca9bf3eaebc00f17f7ccc2954004f2a7d                                                                                   0.2s
 => [build 2/6] WORKDIR /app                                                                                                                                                0.4s
 => [build 3/6] COPY app/package*.json ./                                                                                                                                   0.1s
 => [build 4/6] RUN npm install                                                                                                                                            24.6s
 => [build 5/6] COPY app/ ./                                                                                                                                                2.1s
 => [build 6/6] RUN npm run build                                                                                                                                           4.5s
 => [stage-1 2/2] COPY --from=build /app/build /usr/share/nginx/html                                                                                                        0.0s
 => exporting to image                                                                                                                                                      0.0s
 => => exporting layers                                                                                                                                                     0.0s
 => => writing image sha256:97a7bab41c1411a7641f324edc4412d163b0f09697fa1e503e069d61d7fe2e8c                                                                                0.0s
 => => naming to docker.io/library/react-nginx-app  
```

Mon port 8080 était déjà utilisé à cause des exercices précédent, je peux changer de port d'écoute ou faire du nettoyage. J'ai décider de faire le ménage :

```
rocky@pacman:~/Documents/DevOps/TP3$ sudo lsof -i :8080
COMMAND    PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
docker-pr 2997 root    7u  IPv4  24100      0t0  TCP *:http-alt (LISTEN)
docker-pr 3003 root    7u  IPv6  24101      0t0  TCP *:http-alt (LISTEN)
```
```
rocky@pacman:~/Documents/DevOps/TP3$ docker ps
CONTAINER ID   IMAGE                   COMMAND                  CREATED        STATUS       PORTS                                     NAMES
514792e4bb08   phpmyadmin/phpmyadmin   "/docker-entrypoint.…"   17 hours ago   Up 3 hours   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   devops-phpmyadmin-1
7bbb5cb15014   mysql:5.7               "docker-entrypoint.s…"   17 hours ago   Up 3 hours   3306/tcp, 33060/tcp                       devops-mysql-1
```
```
rocky@pacman:~/Documents/DevOps/TP3$ docker stop 514792e4bb08
514792e4bb08
```
```
rocky@pacman:~/Documents/DevOps/TP3$ docker run -d -p 8080:80 react-nginx-app
0b11dd9eabf87cdbd85283c4a52b6164c9a0d7da7a465a7c4086f8c64657f71f
```
Nous pouvons constater que le projet react c'est bien lancé :

[affichage projet react](../captures/react_8080.png)

On observe bien le même résultat que lors de la question 3.