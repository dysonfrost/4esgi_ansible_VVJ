# Ansible - Bind DNS (Projet d'études)
- Projet ansible
- Auteur: Jérémy REISSER
- Date: 15 nov. 2017

## Description
Ce script ansible permet de déployer 2 serveurs DNS bind (Primaire/Secondaire) avec un transfert de zone.
Il est capable de s'adapter en fonction de la distribution du serveur (RedHat ou Debian).
Un firewall iptables à été configuré pour laisser les ports 22(tcp),80(tcp),443(tcp) et 53(tcp/udp) ouverts.
Un compte de service bind a été créé afin de chroot sur le répertoire qui contient les fichiers de configuration.
Plusieurs tests sont présents tout au long du déroulement du script avec l'affichage d'un message en mode debug
afin de cibler plus facilement tout problème de configuration.

### Petite modification à effectuer sur ansible.cfg :

Ansible se connecte aux serveurs distants avec un utilisateur qui ne possède aucun droit par défaut (avant l'installation de sudo)
Par conséquent je dois utiliser la fonction 'become_method: su' pour effectuer certaines tâches en tant que root.

Pour que Ansible ne me demande pas le mot de passe root, j'avais le choix entre 2 solutions :

- créer un fichier qui contient le mot de passe root en clair
- créer ce même fichier mais en chiffrant le mot de passe à l'aide de vault

Je ne souhaite pas avoir le mot de passe de root en clair dans un fichier, j'ai choisi la 2nde solution mais elle a un inconvénient.
Pour que Ansible puisse déchiffrer le contenu du fichier, vault utilise un mot de passe.

Je peux soit ajouter l'option --ask-vault-pass (ansible va me demander un mot de passe avant d'executer le script)
ou alors utiliser l'option --vault-password-file en indiquant le chemin d'un fichier qui contient le mot de passe vault.


J'ai choisi la seconde option en créant un fichier caché que seul le compte de service ansible peut utiliser avec les droits 0440.
Son contenu est connu uniquement de root et de ansible.

Pour que ansible ait connaissance de ce fichier, j'ai rajouté la ligne suivante dans le fichier /etc/ansible/ansible.cfg :

+ vault_password_file= .vault_pass

.vault_pass étant le nom du fichier qui contient le mot de passe vault.
