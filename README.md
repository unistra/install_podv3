## Playbook Ansible pour déployer et mettre à jour POD v3 
basé sur https://github.com/Furiouss38/pod_install
Ce déploiement d'appuie sur https://www.esup-portail.org/wiki/display/ES/Installation+de+la+plateforme+Pod+V3

### Créer son inventaire à partir de inventories/test/template.yml -> podv3-test.yml
Adapter les variables à son environnement.

### Modifier le fichier de settings  templates/settings/<versionpod>/settings_local.py
Adapter les variables à son environnement.

### Copier les fichiers certificats et clés dans files

### Fichier group_vars/all/vars.yml
Adapter les variables à son environnement.

### Généation des vault afin de crypter les données sensibles
ansible-vault encrypt_string --vault-password-file ~/ansible/.vault_podv2.txt '*****' --name 'bind_pwd'

### procédure d'install :

#### Installation du user pod,des paquets et création de l'environnement virtuel
```
ansible-playbook --inventory ./inventories/test/podv3-test.yml --tags "user,packages,dir,python" --vault-password-file ~/.vault-podv2.txt podv3_deploy.yml -e "user_bastion=<username>"
```

#### Récupeation du code
```
ansible-playbook --inventory ./inventories/test/podv3-test.yml --tags "git" --vault-password-file ~/.vault-podv2.txt podv3_deploy.yml -e "user_bastion=<username>"
```

Si vous êtes en elastisearch 7, il faudra modifier la version d'elasticsearch dans requirements.txt.

#### Rédis
```
ansible-playbook --inventory ./inventories/test/podv3-test.yml --tags "redis" --vault-password-file ~/.vault-podv2.txt podv3_deploy.yml -e "user_bastion=<username>"
```

#### Applications tierces
```
ansible-playbook --inventory ./inventories/test/podv3-test.yml --tags "tierces" --vault-password-file ~/.vault-podv2.txt podv3_deploy.yml -e "user_bastion=<username>"
```
#### Elasticsearch
```
ansible-playbook --inventory ./inventories/test/podv3-test.yml --tags "elasticsearch,settings" --vault-password-file ~/.vault-podv2.txt podv3_deploy.yml -e "user_bastion=<username>"
```
Vérifier que l'index a été installé sinon l'installer manuellement.

#### ffmpeg, ffmpegthumbnailer et imagemagick
```
ansible-playbook --inventory ./inventories/test/podv3-test.yml --tags "encode" --vault-password-file ~/.vault-podv2.txt podv3_deploy.yml -e "user_bastion=<username>"
```

#### Installation des dépendances
```
ansible-playbook --inventory ./inventories/test/podv3-test.yml --tags "dependancies" --vault-password-file ~/.vault-podv2.txt podv3_deploy.yml -e "user_bastion=<username>"
```
#### Installation de la base
```
ansible-playbook --inventory ./inventories/test/podv3-test.yml --tags "postgresql" --vault-password-file ~/.vault-podv2.txt podv3_deploy.yml -e "user_bastion=<username>"
```

#### Initialisation de la base de données
```
ansible-playbook --inventory ./inventories/test/podv3-test.yml --tags "initdb" --vault-password-file ~/.vault-podv2.txt podv3_deploy.yml -e "user_bastion=<username>"
```
Vérifier que les tables sont bien existantes dans la base de données
#### Initialisation de l'application
```
ansible-playbook --inventory ./inventories/test/podv3-test.yml --tags "initapp" --vault-password-file ~/.vault-podv2.txt podv3_deploy.yml -e "user_bastion=<username>"
```
Vérifier que les fichiers statics ont bien été déployés sinon le faire manuellement.
```
(django_pod3) pod@pod:~/django_projects/podv3$ python manage.py collectstatic --no-input --clear
```
#### Deploiement uwsgi et nginx
```
ansible-playbook --inventory ./inventories/test/podv3-test.yml --tags "nginxuwsgi,ssl" --vault-password-file ~/.vault-podv2.txt podv3_deploy.yml -e "user_bastion=<username>"
```

#### Eventuelles erreurs
J'ai du installer django-redis-sessions suite à une erreur
```
pip install django-redis-sessions
```








