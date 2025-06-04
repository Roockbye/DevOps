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