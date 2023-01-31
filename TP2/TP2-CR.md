# TP2 - GITHUB ACTIONS

Alternatives à Github Actions : Jenkins, Gitlab CI, Bitbucket Pipelines.  
Les fichiers __YAML__ décrivent les étapes qu'on veut voir s'exécuter le long de la pipeline.  

`mvn clean verify` : Build et run les tests. Il faut la lancer depuis pom.xml ou ajouter l'argument `--file /path/to/pom.xml`.  
__À quoi sert cette commande ?__
Elle permet dans un premier temps de nettoyer (clear) les anciens builds encore présents dans le cache (sinon on pourrait être confrontés à des comportements inattendus car __MAVEN__ ne reconstruit pas complètement l'application de A à Z), puis de rebuild complètement l'application et enfin d'exécuter les __tests unitaires et d'intégration__ (aussi appelés __component tests__).  

__Unit Test :__ Procédure permettant de vérifier le bon fonctionnement d'une partie précise d'un logiciel ou d'une portion d'un programme.  
__Integration Test :__ Phase de tests, précédée par les tests unitaires et généralement suivie par les tests de validation, où  chacun des modules indépendants du logiciel est assemblé et testé dans l’ensemble.  
__Component Tests :__ Aussi appelés "closed-box testing", ces tests évaluent le comportement du programme sans tenir compte des détails du code sous-jacent. Ils sont effectués sur la section de code dans son intégralité, une fois le développement terminé.  

__Question 2-1 - Qu'est-ce que testcontainers?__  

Dans cette portion de code, on voit que les testcontainers sont utilisées comme des dépendances. C'est une librairie Java qui permet d'effectuer différents types de tests : Elle utilise JUnit Tests, fournit des instances légères et jetables de bases de données communes, de navigateurs Web Selenium ou de tout autre élément pouvant s'exécuter dans un conteneur Docker.

Elle permet de rendre les tests suivants plus faciles : 
- Data access layer integration tests
- Application integration tests
- UI/Acceptance tests
- ...

En bref, ces testcontainers sont des bibliothèques Java qui permettent d'exécuter des conteneurs Docker pendant les tests.

__Créer une première CI qui permet de build et test l'app à chaque fois que qqun commit rt push du code sur le repos :__

1. Créer un dossier .github/workflows, y ajouter main.yml
__main.yml__ : Fichier qui contient l'architecture de notre pipeline. Chaque _job_ représente une étape de ce qu'on veut faire et sera exécuté en parallèle à moins que des liens ne soient spécifiés.

Liste des event qui peuvent trigger un workflow : https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows 
