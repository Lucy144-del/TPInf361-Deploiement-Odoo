Rapport de Déploiement INF3611
Étudiant : DJOUTA WAMEGNE LUCIANA
Matricule : 23V2283
Application : Odoo (Business Suite)
URL : https://23v2283.systeme-res30.app

1. Description de l'application

Odoo est un Progiciel de Gestion Intégré (ERP) open-source utilisé par les entreprises pour centraliser leurs opérations (ventes, comptabilité, stocks, RH).

Cas d'usage : Automatisation complète du flux métier, de la commande client jusqu'à l'écriture comptable, sur une plateforme unique.
2. Architecture des services

Le déploiement utilise une architecture multi-conteneurs orchestrée par Docker Compose avec un rebond sur le Nginx système du VPS :

    db : PostgreSQL (v15) pour le stockage des données.

    odoo : Serveur d'application (Port interne : 8069).

    nginx (Docker) : Proxy interne facilitant la liaison Docker.

    certbot : Gestionnaire de certificats SSL Let's Encrypt.

    nginx (Système) : Reverse proxy global gérant le masquage du port 5880 et l'accès HTTPS final.

Schéma de communication :
Plaintext

[Utilisateur] HTTPS (443) → [Nginx Global] → [Nginx Docker (5880)] → [Odoo (8069)]

3. Structure du projet
Plaintext

odoo/
├── Capture d'ecrans/           # Preuves de fonctionnement (.png)
├── docker-compose.yml          # Orchestration des services
├── .env.example                # Modèle des variables (à versionner)
├── .env                        # Variables réelles (EXCLU du versioning)
├── .gitignore                  # Exclusion du fichier .env et des données
├── nginx.conf                  # Configuration du proxy Docker
└── README.md                   # Documentation

4. Configuration des variables d'environnement

Par mesure de sécurité, les secrets sont stockés dans un fichier .env non versionné. Un fichier .env.example est fourni avec la structure suivante :
Plaintext

# === BASE DE DONNÉES ===
DB_PASSWORD=votre_mot_de_passe_fort_postgres

# === DOMAINE ===
DOMAIN=23v2283.systeme-res30.app
CERTBOT_EMAIL=votre.email@domaine.com

# === SMTP ===
SMTP_SERVER=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=votre.email@gmail.com
SMTP_PASSWORD=votre_mot_de_passe_application_gmail

# === ODOO ADMIN ===
ODOO_ADMIN_PASSWORD=votre_mot_de_passe_admin_odoo

5. Persistance des données

Utilisation de "bind mounts" pour garantir la survie des données aux redémarrages :

    ./postgres_data (Base de données)

    ./odoo_app/config & ./odoo_app/data (Config et fichiers Odoo)

6. Instructions de déploiement
1. Préparation de l'environnement
Bash

# Cloner le dépôt
git clone <votre-depot-url>
cd odoo-deployment

# Créer le fichier .env à partir du modèle
cp .env.example .env

# Éditer les variables avec vos propres valeurs
nano .env

2. Lancement des services
Bash

# Construire et démarrer les conteneurs en arrière-plan
docker compose up -d

# Vérifier que tous les services sont "Up"
docker compose ps

3. Configuration du Reverse Proxy Global

Le Nginx du système doit être configuré pour rediriger le trafic du port 443 vers le port 5880 du projet.
Bash

sudo nano /etc/nginx/sites-available/23v2283.systeme-res30.app
# Appliquer les changements
sudo systemctl reload nginx

7. Commandes de maintenance utiles
Bash

# Voir les logs en temps réel pour le débogage
docker compose logs -f odoo

# Redémarrer uniquement le service applicatif
docker compose restart odoo

# Arrêter tous les services proprement
docker compose down

# Forcer la reconstruction après modification du nginx.conf
docker compose up -d --force-recreate

8. Analyse de rentabilité (Business Case)

    SaaS : Abonnement mensuel (50 000 - 150 000 FCFA).

    Intégration : Déploiement et formation (300 000 - 500 000 FCFA).

    Marge brute : Très élevée (>90%) grâce à la mutualisation des ressources sur VPS.

9. Captures d'écran (Preuves de succès)

Les fichiers suivants sont situés dans le dossier Capture d'ecrans/ :

    config_globale.png : Preuve de la configuration du Nginx système pour le masquage du port.

    conteneurs_actifs.png : Capture montrant tous les services Docker fonctionnels.

    interface_odoo.png : Vue d'Odoo accessible en HTTPS sur l'URL propre.
