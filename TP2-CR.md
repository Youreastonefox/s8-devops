# TP2 - GITHUB ACTIONS

Alternatives à Github Actions : Jenkins, Gitlab CI, Bitbucket Pipelines.  
Les fichiers **YAML** décrivent les étapes qu'on veut voir s'exécuter le long de la pipeline.

`mvn clean verify` : Build et run les tests. Il faut la lancer depuis pom.xml ou ajouter l'argument `--file /path/to/pom.xml`.  
**À quoi sert cette commande ?**
Elle permet dans un premier temps de nettoyer (clear) les anciens builds encore présents dans le cache (sinon on pourrait être confrontés à des comportements inattendus car **MAVEN** ne reconstruit pas complètement l'application de A à Z), puis de rebuild complètement l'application et enfin d'exécuter les **tests unitaires et d'intégration** (aussi appelés **component tests**).

**Unit Test :** Procédure permettant de vérifier le bon fonctionnement d'une partie précise d'un logiciel ou d'une portion d'un programme.  
**Integration Test :** Phase de tests, précédée par les tests unitaires et généralement suivie par les tests de validation, où chacun des modules indépendants du logiciel est assemblé et testé dans l’ensemble.  
**Component Tests :** Aussi appelés "closed-box testing", ces tests évaluent le comportement du programme sans tenir compte des détails du code sous-jacent. Ils sont effectués sur la section de code dans son intégralité, une fois le développement terminé.

**Question 2-1 - Qu'est-ce que testcontainers?**

Dans cette portion de code, on voit que les testcontainers sont utilisées comme des dépendances. C'est une librairie Java qui permet d'effectuer différents types de tests : Elle utilise JUnit Tests, fournit des instances légères et jetables de bases de données communes, de navigateurs Web Selenium ou de tout autre élément pouvant s'exécuter dans un conteneur Docker.

Elle permet de rendre les tests suivants plus faciles :

- Data access layer integration tests
- Application integration tests
- UI/Acceptance tests
- ...

En bref, ces testcontainers sont des bibliothèques Java qui permettent d'exécuter des conteneurs Docker pendant les tests.

**Question 2-2 : Créer une CI qui permet de build et test l'application à chaque commit et push sur le repository**

1. Créer un dossier .github/workflows, y ajouter main.yml
   **main.yml** : Fichier qui contient l'architecture de notre pipeline. Chaque _job_ représente une étape de ce qu'on veut faire et sera exécuté en parallèle à moins que des liens ne soient spécifiés.

Description technique d'un workflow : https://docs.github.com/en/actions/using-workflows/about-workflows
Liste des event qui peuvent trigger un workflow : https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows
Lien qui m'a aidé pour le Set up JDK 17 : https://github.com/actions/setup-java

```yaml
name: CI devops 2023

on:
  #to begin you want to launch this job in main and develop
  push:
    branches:
      - "main"
      - "tp2" # Possède le rôle de ma branche develop, dans un soucis de respect de nommage et d'organisation du repos
  pull_request:
    branches:
      - "main"
      - "tp2"

jobs:
  test-backend:
    runs-on: ubuntu-22.04
    steps:
      #checkout your github code using actions/checkout@v2.5.0
      - uses: actions/checkout@v2.5.0

      #do the same with another action (actions/setup-java@v3) that enable to setup jdk 17
      - name: Set up JDK 17
        uses: actions/setup-java@v3.9.0
        with:
          distribution: corretto # Distribution obligatoire, choix de celle utilisée dans l'API
          java-version: "17" # Utilisation JDK 17

      #finally build your app with the latest command
      - name: Build and test with Maven
        run: mvn clean verify --file ./api/pom.xml # Ajout du chemin vers le fichier pom.xml de l'API
```

**Première CI réussie :**
![](images/CI1-réussi.png)


__Question 2-3 :__

Objectif : Créer et sauvegarder une image Docker contenant notre application sur le Docker Hub à chaque commit sur main.

On va utiliser des __variables d'environnements__ sécurisées dans Github pour stocker nos identifiants, plutôt que dans le code du repos.
Ces variables sont sécurisées car elles sont encryptées par Github, et pas exposées en ligne dans un programme.
Dans notre cas, on ajoute nos identifiants pour Docker Hub.

![](images/secrets.png)


TODO: __Why did we put needs: build-and-test-backend on this job? Maybe try without this and you will see!__