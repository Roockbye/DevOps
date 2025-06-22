# Les pratiques devOps sur lesquels j'ai bossé ou entendu parler

## 1. Développement d'une app web interne

### Ce qui à été fait : 
Souvent ça a été des petites applications web pour gérer des trucs en interne comme un système de ticketing ou un intranet basique pour les utilisateurs. J'ai touché un peu au code, à la mise en place de l'authentification et à la base de données.

### On aurait pu faire mieux :

- **Sécurité** : 

Au début on se contentait souvent d'un truc un peu "léger" sur l'authentification. On aurait dû penser à la sécurité dès le début (avec les failles OWASP Top 10 : injection SQL, XSS etc... qui sont des failles très simples) et utiliser du HTTPS, bien gérer les sessions, hacher les mots de passe... C'est souvent venu après coup, quand on s'est rendu compte des risques. C'était d'abord on code vite et on voit après.

- **Tests** : 

On ne faisait pas assez de tests automatisés. Du coup, les bugs et les failles de sécurité étaient découverts super tard, parfois en production, ce qui était la panique ! Et pas une bonne pratique du tout

- **Déploiement** : 

Le déploiement c'était souvent manuel, un peu à l'arrache. C'était long, il y avait plein d'erreurs possibles et chaque environnement (dev, test, prod) était un peu différent. D'ou la phrase : "Mais sur mon poste ça marche"

## 2. Mettre en place ou améliorer la supervision des serveurs

### Ce qui à été fait: 
J'ai aidé à configurer des outils comme Zabbix, à mettre des alertes pour savoir quand un serveur était en PLS ou à faire des tableaux de bord pour suivre l'état de l'infra.

### On aurait pu faire mieux :

- **Automatisation** : 

La configuration c'était souvent du manuel. On aurait gagné un temps fou à utiliser de l'Infrastructure as Code (IaC) (comme Ansible) pour que ce soit automatisé et reproductible.

- **Alertes** : 

On avait soit trop d'alertes qui servaient à rien ("alertes fatigues"), soit pas assez sur les vrais problèmes. Il fallait vraiment affiner les alertes pour qu'elles soient pertinentes et qu'elles nous remontent les infos là où il faut (Slack, e-mail).

## 3. Participer à une migration vers le cloud (AWS)

### Ce qui à été fait : 
Parfois, j'ai aidé à déplacer des services ou des bases de données vers le cloud. C'était souvent configurer des machines virtuelles et s'assurer que ça tourne.

### On aurait pu faire mieux :

- **Coûts** : 

On se contentait souvent de "migrer tel quel" sans vraiment optimiser les coûts. On aurait pu mieux choisir la taille des machines, utiliser des services managés etc... pour que ça coûte moins cher.

- **Sécurité cloud** : 

La sécurité dans le cloud, c'est un monde à part. Il fallait vraiment se pencher sur la gestion des accès (IAM), la sécurité réseau et le chiffrement des données. C'est facile de faire des erreurs si on ne connaît pas les spécificités.

- **Automatisation** : 
Encore une fois, la provision de ressources était souvent manuelle. L'IaC (avec Terraform par exemple) est hyper utile pour ça.

# Bonnes et mauvaises pratiques DevOps

## Les points positifs 
### 1. Intégration Continue (CI)

Quand les développeurs mettent leur code dans un dépôt commun (Git) **super souvent** et que ça lance automatiquement des tests et des compilations.

On voit tout de suite si le code casse quelque chose ou s'il y a des erreurs. Ça détecte les problèmes hyper vite et ça force à écrire un code plus propre.

### 2. Livraison Continue (CD) / Déploiement Continu (CD)

Une fois que le code passe la CI, il est préparé automatiquement pour être déployé (Livraison) ou carrément déployé en production (Déploiement).

Ça rend les mises à jour hyper fluides. On peut déployer de nouvelles fonctionnalités super rapidement et les utilisateurs les ont vite. Ça réduit le stress des mises en prod car c'est automatisé et donc moins sujet aux erreurs.

### 3. Infrastructure as Code (IaC)

On définit notre infrastructure (serveurs, bases de données, réseaux) dans du code, plutôt que de tout faire à la main.

Ça garantit que tous les environnements sont identiques (plus de "ça marche sur ma machine, pas sur la prod !"). Et on peut reproduire une infrastructure en un clin d'œil, ce qui est parfait pour les tests ou si un truc plante. C'est aussi bien pour la sécurité, car c'est versionné et on voit tous les changements.

### 4. Surveillance

On collecte des données (métriques, logs, traces) pour comprendre comment les systèmes fonctionnent en production et repérer les soucis avant qu'ils ne deviennent critiques.

Ça aide à comprendre ce qui se passe réellement dans les systèmes. On apprend à lire les graphiques et les logs pour savoir si un serveur est lent ou si une appli bug. 

### 5. Sécurité

Intégrer la sécurité dès le début du développement, pas juste à la fin...

Détecter une faille au début, c'est beaucoup moins cher et plus simple à corriger que quand l'appli est déjà en production. On utilise des outils qui scannent notre code dès qu'on le pousse.

### 6. Collaboration et communication

Les équipes dev, ops et sécurité qui bossent main dans la main, communiquent beaucoup.

Ça aide à mieux comprendre les contraintes de chacun. Quand il y a un problème, on le résout beaucoup plus vite parce qu'on parle directement aux bonnes personnes.

## Les points négatifs

### 1. Déploiements manuels et compliqués

Il faut suivre des étapes interminables à la main pour mettre une appli en production.

On fait facilement des erreurs, ça prend un temps fou et c'est super stressant. Chaque déploiement peut être différent et c'est un cauchemar pour trouver la source d'un problème.

### 2. "ça marche sur ma machine"

Les environnements de dev, test et prod ne sont pas les mêmes.

On passe des heures à débuguer des problèmes qui n'existent que sur un environnement spécifique et sont impossibles à reproduire chez nous. Ça retarde tout.

### 3. Sécurité

La sécurité n'est pensée qu'à la fin du projet, juste avant le déploiement.

Si on trouve des failles à la fin, ça coûte une fortune à corriger, parfois il faut tout refaire. Et en plus on risque de mettre une application avec des vulnérabilités critiques en production.

### 4. Manque de tests automatisés

On déploie le code sans avoir assez de tests automatiques (unitaires, intégration, fonctionnels).

Une nouvelle fonctionnalité peut casser un truc qui marchait avant sans qu'on le sache. On doit faire beaucoup de tests manuels, ce qui est embetant et ne couvre pas tout.

### 5. Alertes fatigues

Le système de supervision envoie des tonnes d'alertes non pertinentes.

On finit par ignorer toutes les alertes, même les importantes. C'est épuisant de trier le vrai du faux et on risque de rater un vrai problème parce qu'on est submergé.

### 6. Mauvaise gestion des secrets

Mettre les mots de passe, clés API etc... directement dans le code ou dans des fichiers non sécurisés. C'est beaucoup plus courant que ce qu'on croit

Si le code fuit ou si quelqu'un a accès à ces fichiers, tout peut être compromis.