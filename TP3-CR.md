# TD3 - NOTES

Ansible : Outil qui permet de gérer nos serveurs, les approvisionner et déployer nos applications dessus.  
Alternatives : Chef, Puppet, Terraform.

Utilisation de WSL  
`apt-get install ansible`

serveur : **louisa.chaduc.takima.cloud**

SSH - Secure Shell : C'est un logiciel et un protocole de communication qui utilise le protocole/port TCP/22 de notre machine pour communiquer. C'est sécurisé car la communication est encryptée avec notre clé SSH Privée.

`ssh -i id_rsa centos@louisa.chaduc.takima.cloud`
TODO: Screen du résultat de la commande

Savoir établir une connexion SSH distante c'est essentiellement ce que fait Ansible pour communiquer avec notre serveur. Et en plus Ansible nécessitera certaines configurations supplémentaires pour pouvoir accéder à votre machine.

`ansible all -m ping --private-key=id_rsa -u centos`  
TODO: Screen du ping pong

Demande à Ansible d'installer Apache pour faire un webserver : `ansible all -m yum -a "name=httpd state=present" --private-key=id_rsa -u centos --become`  
TODO: Screen du résultat de la commande
`--become` : Dit à Ansible de passer cette cmd en super user.  
Création page HTML pour notre website : `ansible all -m shell -a 'echo "<html><h1>Hello Louisa</h1></html>" >> /var/www/html/index.html' --private-key=id_rsa -u centos --become`  
TODO: Screen du résultat de la commande
Démarrage du service Apache : `ansible all -m service -a "name=httpd state=started" --private-key=id_rsa -u centos --become`
TODO: Screen du résultat de la commande

# TP3 - ANSIBLE

**Objectif :** Installez et déployez une application automatiquement avec ansible.

## INVENTORY

**Question 3-1 : Documentation de notre inventory des des commandes de base**

### Premier inventaire

Création de mon premier inventaire - `setup.yml` :

```
all:
  vars:
    ansible_user: centos
    ansible_ssh_private_key_file: ../id_rsa
  children:
    prod:
      hosts: hostname or IP
```

Test de l'inventaire : `ansible all -i inventories/setup.yml -m ping`

TODO: Screen du résultat de la commande

### Facts 

**Les facts :** Ce sont des variables contenant des informations sur l'hôte. Il sont préfixés pas `ansible_` et représentent des informations dérivées des conversations avec les systèmes distants.

Obtenir la distribution de son OS : `ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"`  
TODO: Screen du résultat de la commande

Retirer Apache : `ansible all -i inventories/setup.yml -m yum -a "name=httpd state=absent" --become`
TODO: Screen du résultat de la commande

TODO: Rerun les deux dernières commandes et voir la différebce depuis qu'on a retiré Apache

## PLAYBOOKS

**Question 3-2 : Documentation du playbook**

### Premier Playbook 

Création de mon premier inventaire - `playbook.yml` :

```

```

Exécution du playbook : `ansible-playbook -i inventories/setup.yml first.playbook.yml`
`--syntax-check` : Préfixe à ajouter pour tester les playbook avant des les exécuter.  

TODO: Screen du résultat de la commande

### Playbook avancé

Ce playbook nous servira à installer Docker sur notre serveur. 

TODO: Documenter le playbook Docker

TODO: `ansible-playbook -i inventories/setup.yml docker.playbook.yml` puis vérifier qu'une task a bien été créée avec une commande ad-hoc ou en se connectant au serveur.

### Utiliser des rôles

