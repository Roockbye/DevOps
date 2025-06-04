## 2. Compléter le Dockerfile afin de builder correctement l’application contenu dans src/

### a. Une option de npm vous permet de n’installer que ce qui est nécessaire. Quelle est cette option ? Quelle bonne pratique Docker permet t-elle de respecter ?

L'option de npm qui permet de n'installer que ce qui est nécessaire est :`--only=production` ou aussi nommé `--only=prod`

Elle permet de respecter la bonne pratique Docker du minimum d'installation. Ça permet ne pas inclure les dépendances de développement dans l’image finale (qui est lourde).
Moins de taille donc plus léger, moins de surface d’attaque car moins de dépendance possiblement non à jour et surtout plus rapide au run. On installe pas **devDependencies**.

https://betterstack.com/community/questions/how-to-prevent-install-of-devdependencies-in-node/

### 3. A l’aide de la commande docker build, créer l’image ma_super_app

### 4. Compléter le fichier docker-compose.yml afin d’éxécuter ma_super_app avec sa base de données.