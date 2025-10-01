# Procédure de Déploiement

Décrivez ci-dessous votre procédure de déploiement en détaillant chacune des étapes. De la préparation du VPS à la méthodologie de déploiement continu.

## Préparation du VPS

Je me suis d'abord connecté à mon serveur via CMD de windows.
ssh root@172.17.4.31

J'ai installé Nginx :
sudo apt install nginx

J'ai installé les pré-requis pour docker :
apt install -y curl wget git unzip software-properties-common apt-transport-https ca-certificates gnupg lsb-release

J'ai été sur la documentation officielle de docker pour installer docker sur mon serveur (https://docs.docker.com/engine/install/debian/) :

Ajouter la clé GPG officielle de Docker
curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

Ajouter le repository Docker
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

Installer Docker
apt update
apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

Démarrer et activer Docker
systemctl start docker
systemctl enable docker

J'ai ensuite installer docker compose en suivant la document Docker.
J'ai crée et ajouté un utilisateur :
adduser user
usermod -aG docker user

Puis j'ai vérifié si tout était correctement installé :
docker --version : Docker version 28.4.0, build d8eb465
docker compose version : Docker Compose version v2.39.4

J'ai arrêté et désactivé le nginx installé directement sur le serveur car Docker utilisera le port 80 :
systemctl stop nginx
systemctl disable nginx

## Méthode de déploiement

1. Transférer les fichiers sur le serveur

Depuis ma machine locale, je transfère le projet vers le serveur :

```bash
scp -r . root@172.17.4.31:/var/www/habit-tracker
```

Ou avec Git (recommandé) :

```bash
ssh root@172.17.4.31
cd /var/www
git clone <url-du-repo> habit-tracker
cd habit-tracker
```

Configuration de l'environnement

Sur le serveur, créer le fichier `.env` :

```bash
cd /var/www/habit-tracker
nano .env
```

Contenu du fichier `.env` :

```
# Application
APP_ENV=production
APP_DEBUG=false

# Database Configuration
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=habit_tracker
DB_USERNAME=habit_user
DB_PASSWORD=mdpsecurise

# MySQL Root Password
MYSQL_ROOT_PASSWORD=mdpsecurise
```

Préparer les dossiers de logs

mkdir -p logs/nginx logs/mysql
chmod -R 755 logs/

Installer les dépendances PHP (Composer)

Installer Composer sur le serveur :

curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer


Installer les dépendances du projet :

cd /var/www/habit-tracker
composer install --no-dev --optimize-autoloader

Lancer les conteneurs Docker

cd /var/www/habit-tracker
docker compose up -d

Vérifier que les conteneurs sont bien lancés :

docker compose ps


Initialiser la base de données

Attendre quelques secondes que MySQL soit prêt, puis créer la base de données :

docker compose exec php php bin/create-database


Charger les données de démonstration (optionnel) :

docker compose exec php php bin/load-demo-data

Vérifier le déploiement

Tester l'application :

curl http://172.17.4.31


Ou depuis un navigateur : `http://172.17.4.31`

Vérifier les logs

Si des problèmes surviennent :

docker compose logs nginx

docker compose logs php

docker compose logs mysql

docker compose logs
