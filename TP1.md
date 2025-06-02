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

Dockerfile
```
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html
```
```
rocky@pacman:~/Documents/DevOps$ docker build -t mon-nginx .
[+] Building 0.1s (7/7) FINISHED                                                                                                                                  docker:default
 => [internal] load build definition from Dockerfile                                                                                                                        0.0s
 => => transferring dockerfile: 103B                                                                                                                                        0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                                                                                             0.0s
 => [internal] load .dockerignore                                                                                                                                           0.0s
 => => transferring context: 2B                                                                                                                                             0.0s
 => [internal] load build context                                                                                                                                           0.0s
 => => transferring context: 182B                                                                                                                                           0.0s
 => [1/2] FROM docker.io/library/nginx:latest                                                                                                                               0.0s
 => [2/2] COPY index.html /usr/share/nginx/html/index.html                                                                                                                  0.0s
 => exporting to image                                                                                                                                                      0.0s
 => => exporting layers                                                                                                                                                     0.0s
 => => writing image sha256:2743929e6f93b0277c4203bb430055eab76622131915a96c5b0bda0d21f62d31                                                                                0.0s
 => => naming to docker.io/library/mon-nginx                                                                                                                                0.0s
rocky@pacman:~/Documents/DevOps$ ls
Dockerfile  index.html  nginx.png  README.md  TP1.md
```

### b. Exécuter cette nouvelle image de manière à servir la page html (commande docker run)

```
rocky@pacman:~/Documents/DevOps$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED          STATUS                  PORTS                                     NAMES
859d2bd59d79   mon-nginx              "/docker-entrypoint.…"   58 seconds ago   Created                                                           nginx-dockerfile
438337c3df45   nginx                  "/docker-entrypoint.…"   45 minutes ago   Up 45 minutes           0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   serveur-web
69c7efef5bae   nwodtuhs/exegol:full   "/bin/bash /.exegol/…"   9 days ago       Exited (0) 9 days ago                                             exegol-STHACK
rocky@pacman:~/Documents/DevOps$ docker run --name custom-nginx -d -p 8085:80 mon-nginx
d33578721bd0e05a6ff8e0592b3a84e16ccbce7d19fbbca1a82706687714a633
```
[custom-nginx](./custom-nginx.png)

### c. Quelles différences observez-vous entre les procédures 5. et 6. ? Avantages et inconvénients de l’une et de l’autre méthode ? (Mettre en relation ce qui est observé avec ce qui a été présenté pendant le cours)

La procédure 5 consiste à utiliser une image existante (comme nginx) et à injecter la page HTML soit via un volume avec l’option -v, soit avec la commande docker cp. Cette méthode est rapide à mettre en place, idéale pour les tests ou le développement local, car elle permet de modifier les fichiers sans reconstruire l’image à chaque fois. Cependant, elle n’est pas adaptée aux déploiements professionnels ou automatisés, car l’image obtenue ne contient pas réellement les fichiers nécessaires. Cela rend difficile la reproductibilité, le versionnage ou l’intégration dans une chaîne CI/CD.

La procédure 6 repose sur la création d’une image personnalisée avec un Dockerfile. Grâce à l’instruction COPY, le fichier HTML est inclus directement dans l’image lors de la phase de build. Cette méthode garantit que le comportement du conteneur est stable, versionnable et entièrement reproductible sur n’importe quel environnement. Elle s’intègre bien dans les pratiques DevOps, notamment dans les processus d’intégration et de déploiement continus. En revanche, elle est un peu plus lente à utiliser en développement, car chaque modification du HTML impose de recompiler l’image.

Ainsi, la procédure 5 est davantage orientée vers la phase de développement ou de test rapide, tandis que la procédure 6 est adaptée à l’industrialisation, à la publication sur un registre Docker et à une utilisation dans des environnements de production.

#### Il  faut séparer les environnements dynamiques de développement des environnements stables et automatisés de production.

## 7. Utiliser une base de données dans un conteneur docker

### a. Récupérer les images mysql:5.7 et phpmyadmin depuis le Docker Hub

```
rocky@pacman:~/Documents/DevOps$ docker pull mysql:5.7
5.7: Pulling from library/mysql
20e4dcae4c69: Pulling fs layer 
1c56c3d4ce74: Pull complete 
e9f03a1c24ce: Pull complete 
68c3898c2015: Pull complete 
6b95a940e7b6: Pull complete 
90986bb8de6e: Pull complete 
ae71319cb779: Pull complete 
ffc89e9dfd88: Pull complete 
43d05e938198: Pull complete 
064b2d298fba: Pull complete 
df9a4d85569b: Pull complete 
Digest: sha256:4bc6bc963e6d8443453676cae56536f4b8156d78bae03c0145cbe47c2aad73bb
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
rocky@pacman:~/Documents/DevOps$ docker pull phpmyadmin
Using default tag: latest
latest: Pulling from library/phpmyadmin
61320b01ae5e: Already exists 
b4ce612dc732: Pulling fs layer 
093338982d92: Pulling fs layer 
0c2a0ba9eb0c: Pulling fs layer 
442abaed7751: Pulling fs layer 
aa4e51934eef: Pulling fs layer 
924949db942b: Pulling fs layer 
153d2fb08c64: Pulling fs layer 
26f17ce45149: Pulling fs layer 
64a857ca8a6a: Pulling fs layer 
7443480a6bdb: Pull complete 
d4dfcfe68eba: Pull complete 
91e76e37da73: Pull complete 
4f4fb700ef54: Pull complete 
708105cf3285: Pull complete 
97739253e39f: Pull complete 
6722b89322cb: Pull complete 
9ebc473a2663: Pull complete 
39ff72c5f974: Pull complete 
2daa1390d242: Pull complete 
Digest: sha256:73467128842bc4406372310f068bc9ccb6727a82c7b5dc9c4f3d815ead33eab8
Status: Downloaded newer image for phpmyadmin:latest
docker.io/library/phpmyadmin:latest
```

### b. Exécuter deux conteneurs à partir des images et ajouter une table ainsi que quelques enregistrements dans la base de données à l’aide de phpmyadmin

```
rocky@pacman:~/Documents/DevOps$ docker run --name mysql-server \
  -e MYSQL_ROOT_PASSWORD=root \
  -e MYSQL_DATABASE=db-test \
  -d mysql:5.7
9facdddd67010b8e6ac5ad103e449bf169591ffac426bb20c94600cea1c55133
```

```
rocky@pacman:~/Documents/DevOps$ docker run --name phpmyadmin   -d   --link mysql-server:db   -p 8081:80   -e PMA_HOST=db   phpmyadmin
15238149f1e3350d103b3fbe878985e7d25ab9549eac3f2cf2d3ec0d2fb77606
rocky@pacman:~/Documents/DevOps$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED             STATUS                  PORTS                                     NAMES
15238149f1e3   phpmyadmin             "/docker-entrypoint.…"   3 seconds ago       Up 3 seconds            0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   phpmyadmin
9facdddd6701   mysql:5.7              "docker-entrypoint.s…"   7 minutes ago       Up 7 minutes            3306/tcp, 33060/tcp                       mysql-server
d33578721bd0   mon-nginx              "/docker-entrypoint.…"   30 minutes ago      Up 30 minutes           0.0.0.0:8085->80/tcp, [::]:8085->80/tcp   custom-nginx
859d2bd59d79   mon-nginx              "/docker-entrypoint.…"   32 minutes ago      Created                                                           nginx-dockerfile
438337c3df45   nginx                  "/docker-entrypoint.…"   About an hour ago   Up About an hour        0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   serveur-web
69c7efef5bae   nwodtuhs/exegol:full   "/bin/bash /.exegol/…"   9 days ago          Exited (0) 9 days ago 
```

[connexion phpmyadmin](./phpmyadmin.png)

La communication entre les deux conteneurs fonctionne grâce à l’option `--link`, qui permet à phpMyAdmin de résoudre le nom `db` vers l’IP du conteneur `mysql-server`.

[ajout data mysql](./données.png)

## 8. Faire la même chose que précédemment en utilisant un fichier docker-compose.yml

### a. Qu’apporte le fichier docker-compose par rapport aux commandes docker run ? Pourquoi est-il intéressant ? (cf. ce qui a été présenté pendant le cours)



### b. Quel moyen permet de configurer (premier utilisateur, première base de données, mot de passe root, …) facilement le conteneur mysql au lancement ?

## 9. Observation de l’isolation réseau entre 3 conteneurs

    a. A l’aide de docker-compose et de l’image praqma/network-multitool
    disponible sur le Docker Hub créer 3 services (web, app et db) et 2 réseaux
    (frontend et backend).
    Les services web et db ne devront pas pouvoir effectuer de ping de l’un vers
    l’autre

    b. Quelles lignes du résultat de la commande docker inspect justifient ce
    comportement ?

    c. Dans quelle situation réelles (avec quelles images) pourrait-on avoir cette
    configuration réseau ? Dans quel but ?