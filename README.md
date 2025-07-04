Rôle Ansible : déploiement de WordPress

Ce rôle ansible-role-wordpress automatise entièrement l’installation et la configuration d’un site WordPress avec MariaDB sur des environnements Debian/Ubuntu et RedHat/Rocky Linux.

Table des matières

1. Description

2. Arborescence

3. Variables par défaut

4. Handlers

5. Tâches principales

6. Templates et fichiers

7. Tests


Ce rôle prend en charge :

Mise à jour du système (apt, dnf)

Installation des paquets requis (Apache/httpd, PHP, MariaDB, utilitaires)

Configuration et démarrage d’Apache (ou httpd) et MariaDB

Sécurisation initiale de MariaDB (mot de passe root, suppression comptes anonymes)

Création de la base de données et de l’utilisateur WordPress

Téléchargement et décompression de l’archive WordPress

Génération du fichier wp-config.php à partir d’un template Jinja2

Activation du site et réécriture d’URL (Debian)

Le rôle détecte automatiquement la famille système (ansible_os_family) et adapte les modules, services et chemins.

Arborescence

roles/ansible-role-wordpress/
├── README.md
├── defaults/
│   └── main.yml       # Variables modifiables par l’utilisateur
├── vars/
│   └── main.yml       # Variables constantes
├── tasks/
│   └── main.yml       # Enchaînement des rôles, conditions, boucles
├── handlers/
│   └── main.yml       # Définition des handlers (reload Apache)
├── templates/
│   ├── wordpress.conf.j2  # VirtualHost Apache (Debian)
│   └── wp-config.php.j2   # Configuration WP
├── files/
│   └──               # Fichiers statiques (si besoin)
├── tests/
│   ├── inventory     # Inventaire de test (clients Debian+Rocky)
│   └── test.yml      # Playbook de vérification
└── meta/
    └── main.yml      # Meta-données pour Galaxy (dépendances, plate‑formes)

Variables par défaut (defaults/main.yml)

Nom

Valeur par défaut

Description

db_root_password

examplerootPW

Mot de passe du compte root MariaDB

db_name

wordpress

Nom de la base WordPress

db_user

example

Nom de l’utilisateur WordPress

db_user_password

examplePW

Mot de passe de l’utilisateur WordPress

wordpress_web_dir

/var/www/html

Répertoire racine web

apache_conf_path

/etc/apache2/sites-available/... ou /etc/httpd/conf/httpd.conf

Chemin du fichier de configuration Apache selon OS

Handlers (handlers/main.yml)

Reload Apache : relance le service apache2 ou httpd après modification de la configuration.

Tâches principales (tasks/main.yml)

Mise à jour du système (package avec update_cache ou dnf.update_cache).

Installation des paquets requis (paquets différents selon Debian ou RedHat).

Suppression de la page par défaut d’Apache.

Démarrage d’Apache (Debian) ou httpd (RedHat/Rocky).

Démarrage de MariaDB (service mariadb ou mysqld_safe).

Pause puis vérification de la disponibilité du port 3306.

Sécurisation de MariaDB (commande mysql_secure_installation personnalisée).

Création de la base et de l’utilisateur WordPress via le module mysql_db et mysql_user.

Téléchargement de l’archive WordPress via get_url.

Extraction de l’archive (unarchive).

Génération du wp-config.php à partir du template.

Activation du site et du module rewrite (Debian).

Templates

wordpress.conf.j2 : VirtualHost Apache pour Debian.

wp-config.php.j2 : Fichier de configuration WordPress, avec variables Jinja.

un fichier inventory listant vos containers Ubuntu et Rocky,

un playbook test.yml simple qui utilise le rôle sur hosts: all.

Exécution :
bash\ansible-playbook -i tests/inventory tests/test.yml
