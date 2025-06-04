## 2. Compléter le Dockerfile afin de builder correctement l’application contenu dans src/

voir fichier Dockerfile

### a. Une option de npm vous permet de n’installer que ce qui est nécessaire. Quelle est cette option ? Quelle bonne pratique Docker permet t-elle de respecter ?

L'option de npm qui permet de n'installer que ce qui est nécessaire est :`--only=production` ou aussi nommé `--only=prod`

Elle permet de respecter la bonne pratique Docker du minimum d'installation. Ça permet ne pas inclure les dépendances de développement dans l’image finale (qui est lourde).
Moins de taille donc plus léger, moins de surface d’attaque car moins de dépendance possiblement non à jour et surtout plus rapide au run. On installe pas **devDependencies**.

https://betterstack.com/community/questions/how-to-prevent-install-of-devdependencies-in-node/
https://docs.npmjs.com/cli/v9/commands/npm-install

### 3. A l’aide de la commande docker build, créer l’image ma_super_app

```
rocky@pacman:~/Documents/DevOps/tp-docker-2$ docker build -t ma_super_app .
[+] Building 31.8s (10/10) FINISHED                                                                                                                               docker:default
 => [internal] load build definition from Dockerfile                                                                                                                        0.0s
 => => transferring dockerfile: 181B                                                                                                                                        0.0s
 => [internal] load metadata for docker.io/library/node:12-alpine3.9                                                                                                        8.5s
 => [internal] load .dockerignore                                                                                                                                           0.0s
 => => transferring context: 2B                                                                                                                                             0.0s
 => [1/5] FROM docker.io/library/node:12-alpine3.9@sha256:16d40e6c2858ee41cc7e19bb36f8a92718ad935ceae036e88dcffb68041dea6c                                                  6.1s
 => => resolve docker.io/library/node:12-alpine3.9@sha256:16d40e6c2858ee41cc7e19bb36f8a92718ad935ceae036e88dcffb68041dea6c                                                  0.0s
 => => sha256:16d40e6c2858ee41cc7e19bb36f8a92718ad935ceae036e88dcffb68041dea6c 1.43kB / 1.43kB                                                                              0.0s
 => => sha256:ed9251aca55330890ef48a274c6ce03052e5438e87b6101b0bab5362ac79b5e5 1.16kB / 1.16kB                                                                              0.0s
 => => sha256:a7d6e4c06dd4f80d3ba52db58f3c7421f6c29497ddf084928103d71b6635314c 6.73kB / 6.73kB                                                                              0.0s
 => => sha256:31603596830fc7e56753139f9c2c6bd3759e48a850659506ebfb885d1cf3aef5 2.77MB / 2.77MB                                                                              1.0s
 => => sha256:f5b0d1ce6f59dcbee12d1ce6f4093fa9a7939c7751f00ff2137b4651dcf2de8f 24.49MB / 24.49MB                                                                            5.5s
 => => sha256:55922cf6f31687a003e818aab6c5e42db644b2be19a1c74040d750db3d63354c 2.24MB / 2.24MB                                                                              2.5s
 => => extracting sha256:31603596830fc7e56753139f9c2c6bd3759e48a850659506ebfb885d1cf3aef5                                                                                   0.0s
 => => sha256:e8a7c0650cafa577c71ba40f4423d3486d700990904de11327a69bdff61d1a92 283B / 283B                                                                                  2.4s
 => => extracting sha256:f5b0d1ce6f59dcbee12d1ce6f4093fa9a7939c7751f00ff2137b4651dcf2de8f                                                                                   0.5s
 => => extracting sha256:55922cf6f31687a003e818aab6c5e42db644b2be19a1c74040d750db3d63354c                                                                                   0.0s
 => => extracting sha256:e8a7c0650cafa577c71ba40f4423d3486d700990904de11327a69bdff61d1a92                                                                                   0.0s
 => [internal] load build context                                                                                                                                           0.0s
 => => transferring context: 1.54kB                                                                                                                                         0.0s
 => [2/5] WORKDIR /app                                                                                                                                                      0.1s
 => [3/5] COPY package*.json ./                                                                                                                                             0.0s
 => [4/5] RUN npm install --only=production                                                                                                                                16.6s
 => [5/5] COPY . .                                                                                                                                                          0.0s
 => exporting to image                                                                                                                                                      0.4s
 => => exporting layers                                                                                                                                                     0.4s
 => => writing image sha256:4cf144be5196c113b2d9e759a1d71bc2d83fdae9d7edc3da7478c8fd364dc43d                                                                                0.0s
 => => naming to docker.io/library/ma_super_app 
```
On vérifie qu'elle existe bien

```
rocky@pacman:~/Documents/DevOps/tp-docker-2$ docker images
REPOSITORY                 TAG       IMAGE ID       CREATED          SIZE
ma_super_app               latest    4cf144be5196   50 seconds ago   143MB
mon-nginx                  latest    2743929e6f93   42 hours ago     192MB
nginx                      latest    be69f2940aaf   6 weeks ago      192MB
nwodtuhs/exegol            full      7f9f235e780e   2 months ago     52.3GB
phpmyadmin/phpmyadmin      latest    0276a66ce322   4 months ago     571MB
phpmyadmin                 latest    a227ea564a55   4 months ago     570MB
mysql                      5.7       5107333e08a8   17 months ago    501MB
praqma/network-multitool   latest    1631e536ed7d   3 years ago      39.9MB
```
**ma_super_app** est bien là !

### 4. Compléter le fichier docker-compose.yml afin d’éxécuter ma_super_app avec sa base de données.

```
rocky@pacman:~/Documents/DevOps/tp-docker-2$ docker compose build
WARN[0000] /home/rocky/Documents/DevOps/tp-docker-2/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 
Compose can now delegate builds to bake for better performance.
 To do so, set COMPOSE_BAKE=true.
[+] Building 2.1s (11/11) FINISHED                                                                                                                                docker:default
 => [node internal] load build definition from Dockerfile                                                                                                                   0.0s
 => => transferring dockerfile: 181B                                                                                                                                        0.0s
 => [node internal] load metadata for docker.io/library/node:12-alpine3.9                                                                                                   2.0s
 => [node internal] load .dockerignore                                                                                                                                      0.0s
 => => transferring context: 2B                                                                                                                                             0.0s
 => [node 1/5] FROM docker.io/library/node:12-alpine3.9@sha256:16d40e6c2858ee41cc7e19bb36f8a92718ad935ceae036e88dcffb68041dea6c                                             0.0s
 => [node internal] load build context                                                                                                                                      0.0s
 => => transferring context: 1.66kB                                                                                                                                         0.0s
 => CACHED [node 2/5] WORKDIR /app                                                                                                                                          0.0s
 => CACHED [node 3/5] COPY package*.json ./                                                                                                                                 0.0s
 => CACHED [node 4/5] RUN npm install --only=production                                                                                                                     0.0s
 => [node 5/5] COPY . .                                                                                                                                                     0.0s
 => [node] exporting to image                                                                                                                                               0.0s
 => => exporting layers                                                                                                                                                     0.0s
 => => writing image sha256:843ee34c66d72bd2efe81b13e4c6596ae272196c848fc6a75957008e8c836bc3                                                                                0.0s
 => => naming to docker.io/library/tp-docker-2-node                                                                                                                         0.0s
 => [node] resolving provenance for metadata file                                                                                                                           0.0s
[+] Building 1/1
 ✔ node  Built                             
```

```
rocky@pacman:~/Documents/DevOps/tp-docker-2$ docker compose up -d
WARN[0000] /home/rocky/Documents/DevOps/tp-docker-2/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 
[+] Running 11/11
 ✔ mysql Pulled                                                                                                                                                            30.8s 
[+] Running 3/4
 ✔ Network tp-docker-2_default      Created                                                                                                                                 0.0s 
 ✔ Volume "tp-docker-2_mysql_data"  Created                                                                                                                                 0.0s 
 ⠇ Container mysql                  Starting                                                                                                                                0.8s 
 ✔ Container ma_super_app           Created                                                                                                                                 0.0s 
Error response from daemon: failed to set up container networking: driver failed programming external connectivity on endpoint mysql (122e283091db89e50e29f80231ee286981050a41e79de1696b46e3c52126f992): failed to bind host port for 0.0.0.0:3306:172.21.0.2:3306/tcp: address already in use
```

J'ai déjà utiliser le port pour un autre service, je recommence en changeant le port 3306. Je l'ai changé par le port 3307 sur ma machine

```rocky@pacman:~/Documents/DevOps/tp-docker-2$ docker compose up -d
WARN[0000] /home/rocky/Documents/DevOps/tp-docker-2/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 
[+] Running 2/2
 ✔ Container mysql         Started                                                                                                                                          0.2s 
 ✔ Container ma_super_app  Started 
```

```
rocky@pacman:~/Documents/DevOps/tp-docker-2$ docker ps -a
CONTAINER ID   IMAGE                      COMMAND                  CREATED              STATUS                         PORTS                                     NAMES
3efb3bdc6d31   mysql:8                    "docker-entrypoint.s…"   About a minute ago   Restarting (1) 7 seconds ago                                             mysql
099bbb690891   tp-docker-2-node           "docker-entrypoint.s…"   4 minutes ago        Exited (1) 47 seconds ago                                                ma_super_app
daaeccf4a484   praqma/network-multitool   "/bin/sh /docker/ent…"   16 hours ago         Exited (0) 15 hours ago                                                  db
c60c9b3bcf40   praqma/network-multitool   "/bin/sh /docker/ent…"   16 hours ago         Exited (0) 15 hours ago                                                  web
7e8798b76cc6   praqma/network-multitool   "/bin/sh /docker/ent…"   16 hours ago         Exited (0) 15 hours ago                                                  app
514792e4bb08   phpmyadmin/phpmyadmin      "/docker-entrypoint.…"   16 hours ago         Up 2 hours                     0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   devops-phpmyadmin-1
7bbb5cb15014   mysql:5.7                  "docker-entrypoint.s…"   16 hours ago         Up 2 hours                     3306/tcp, 33060/tcp                       devops-mysql-1
d33578721bd0   mon-nginx                  "/docker-entrypoint.…"   42 hours ago         Exited (0) 42 hours ago                                                  custom-nginx
859d2bd59d79   mon-nginx                  "/docker-entrypoint.…"   42 hours ago         Created                                                                  nginx-dockerfile
438337c3df45   nginx                      "/docker-entrypoint.…"   43 hours ago         Exited (0) 42 hours ago                                                  serveur-web
69c7efef5bae   nwodtuhs/exegol:full       "/bin/bash /.exegol/…"   11 days ago          Exited (0) 11 days ago                                                   exegol-STHACK
rocky@pacman:~/Documents/DevOps/tp-docker-2$ 
```
[ma super app](../captures/ma_super_app.png)