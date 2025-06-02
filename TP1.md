## 5.Exécuter un serveur web (apache, nginx, …) dans un conteneur docker

J'ai choisi NGINX pour ce TP

### a. Récupérer l’image sur le Docker Hub

https://hub.docker.com/_/nginx

```
rocky@pacman:~/Documents/DevOps$ docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
61320b01ae5e: Pull complete 
670a101d432b: Pull complete 
405bd2df85b6: Pull complete 
cc80efff8457: Pull complete 
2b9310b2ee4b: Pull complete 
6c4aa022e8e1: Pull complete 
abddc69cb49d: Pull complete 
Digest: sha256:fb39280b7b9eba5727c884a3c7810002e69e8f961cc373b89c92f14961d903a0
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
```

### b. Vérifier que cette image est présente en local

```
rocky@pacman:~/Documents/DevOps$ docker images
REPOSITORY        TAG       IMAGE ID       CREATED        SIZE
nginx             latest    be69f2940aaf   6 weeks ago    192MB
nwodtuhs/exegol   full      7f9f235e780e   2 months ago   52.3GB
```
(note : exegol est pour un projet perso et à ne pas prendre en compte pour ce cours)

### c. Créer un fichier index.html simple

```
rocky@pacman:~/Documents/DevOps$ mkdir ~/nginx
rocky@pacman:~/Documents/DevOps$ cd ~/nginx
rocky@pacman:~/nginx$ echo "<h1>Mon serveur web NGINX</h1>" > index.html
rocky@pacman:~/nginx$ cat index.html
<h1>Mon serveur web NGINX</h1>
```

### d. Démarrer un conteneur et servir la page html créée précédemment à l’aide d’un volume (option -v de docker run)

```rocky@pacman:~/nginx$ docker run --name nginx -v $(pwd)/index.html:/usr/share/nginx/html/index.html:ro -p 8080:80 -d nginx
e8ba350f40e9fefa63ea6bd9a7fce71811bee7562ec616e083e021d87e172c07
```
[nginx](./nginx.png)

### e. Supprimer le conteneur précédent et arriver au même résultat que précédemment à l’aide de la commande docker cp

```
rocky@pacman:~/nginx$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED         STATUS                  PORTS                                     NAMES
e8ba350f40e9   nginx                  "/docker-entrypoint.…"   5 minutes ago   Up 5 minutes            0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   nginx
69c7efef5bae   nwodtuhs/exegol:full   "/bin/bash /.exegol/…"   9 days ago      Exited (0) 9 days ago                                             exegol-STHACK
rocky@pacman:~/nginx$ docker rm -f e8ba350f40e9
e8ba350f40e9
rocky@pacman:~/nginx$ docker images
REPOSITORY        TAG       IMAGE ID       CREATED        SIZE
nginx             latest    be69f2940aaf   6 weeks ago    192MB
nwodtuhs/exegol   full      7f9f235e780e   2 months ago   52.3GB
rocky@pacman:~/nginx$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED      STATUS                  PORTS     NAMES
69c7efef5bae   nwodtuhs/exegol:full   "/bin/bash /.exegol/…"   9 days ago   Exited (0) 9 days ago             exegol-STHACK
rocky@pacman:~/nginx$ 
```

```
rocky@pacman:~/nginx$ docker run --name serveur-web -p 8080:80 -d nginx
438337c3df452468b5ef05376ddb164b79a3e17ae53f42625ab8e5451c5e3260
rocky@pacman:~/nginx$ docker cp index.html serveur-web:/usr/share/nginx/html/index.html
Successfully copied 2.05kB to serveur-web:/usr/share/nginx/html/index.html
```


## 6. Builder une image

### a. A l’aide d’un Dockerfile, créer une image (commande docker build)

### b. Exécuter cette nouvelle image de manière à servir la page html (commande docker run)

### c. Quelles différences observez-vous entre les procédures 5. et 6. ? Avantages et inconvénients de l’une et de l’autre méthode ? (Mettre en relation ce qui est observé avec ce qui a été présenté pendant le cours)

## 7. Utiliser une base de données dans un conteneur docker

    a. Récupérer les images mysql:5.7 et phpmyadmin depuis le Docker Hub

    b. Exécuter deux conteneurs à partir des images et ajouter une table ainsi que
quelques enregistrements dans la base de données à l’aide de phpmyadmin

8. Faire la même chose que précédemment en utilisant un fichier
docker-compose.yml

    a. Qu’apporte le fichier docker-compose par rapport aux commandes docker run
    ? Pourquoi est-il intéressant ? (cf. ce qui a été présenté pendant le cours)

    b. Quel moyen permet de configurer (premier utilisateur, première base de
    données, mot de passe root, …) facilement le conteneur mysql au lancement ?

9. Observation de l’isolation réseau entre 3 conteneurs

    a. A l’aide de docker-compose et de l’image praqma/network-multitool
    disponible sur le Docker Hub créer 3 services (web, app et db) et 2 réseaux
    (frontend et backend).
    Les services web et db ne devront pas pouvoir effectuer de ping de l’un vers
    l’autre

    b. Quelles lignes du résultat de la commande docker inspect justifient ce
    comportement ?

    c. Dans quelle situation réelles (avec quelles images) pourrait-on avoir cette
    configuration réseau ? Dans quel but ?