Rapport de Déploiement INF3611 - Odoo ERP
Identification

    Étudiant : DJOUTA WAMEGNE LUCIANA

    Matricule : 23V2283

    Application : Odoo (Business Suite)

    Statut : Opérationnel

    URL : https://23v2283.systeme-res30.app

1. Description de l'application

Odoo est un Progiciel de Gestion Intégré (ERP) open-source centralisant les opérations critiques d'une entreprise : ventes, comptabilité, stocks et ressources humaines.

Cas d'usage : Transformation digitale d'une PME par l'automatisation des flux métiers (de la commande client à la génération automatique de la facture et de l'écriture comptable).
2. Architecture des Services

Le déploiement repose sur une architecture multi-conteneurs isolée, interfacée avec un Reverse Proxy système pour une sécurité maximale.
Service	Technologie	Rôle
Database	PostgreSQL 15	Stockage persistant des données métier
Application	Odoo v17	Logique métier et interface utilisateur
Proxy Local	Nginx (Docker)	Gestionnaire de flux interne et headers
Proxy Global	Nginx (Ubuntu)	Terminaison SSL et masquage du port 5880
SSL	Certbot	Automatisation des certificats Let's Encrypt
3. Structure du Projet
Plaintext

odoo_deployment/
├── Capture d'ecrans/           # Preuves formelles de fonctionnement
│   ├── config_globale.png      # Configuration Nginx du VPS
│   ├── conteneurs_actifs.png   # État des services Docker
│   └── interface_odoo.png      # Rendu final en HTTPS
├── docker-compose.yml          # Orchestration des services
├── .env.example                # Modèle des variables d'environnement
├── .env                        # Variables réelles (Exclu par .gitignore)
├── .gitignore                  # Protection des données sensibles
├── nginx.conf                  # Configuration du proxy interne
└── README.md                   # Documentation technique

4. Gestion des Variables d'Environnement

L'application utilise un fichier .env pour isoler les secrets. Un modèle .env.example est fourni pour le déploiement.

Extrait du fichier .env.example :
Bash

# BASE DE DONNEES
DB_PASSWORD=votre_mot_de_passe_postgres

# DOMAINE ET SSL
DOMAIN=23v2283.systeme-res30.app
CERTBOT_EMAIL=votre.email@domaine.com

# CONFIGURATION SMTP
SMTP_SERVER=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=votre.email@gmail.com
SMTP_PASSWORD=votre_mot_de_passe_application

# ADMINISTRATION ODOO
ODOO_ADMIN_PASSWORD=votre_mot_de_passe_admin

5. Instructions de Déploiement
Phase 1 : Préparation

    Cloner le dépôt :
    Bash

    git clone <repository_url>
    cd odoo-deployment

    Initialiser les variables :
    Bash

    cp .env.example .env
    nano .env # Renseigner vos accès

Phase 2 : Lancement

    Démarrer l'infrastructure :
    Bash

    docker compose up -d

    Vérifier l'état des services :
    Bash

    docker compose ps

Phase 3 : Configuration Réseau

Le Nginx système doit rediriger le port 443 vers le port 5880 en utilisant les directives proxy_pass et proxy_set_header pour maintenir l'URL propre sans affichage du port technique.
6. Commandes de Maintenance

    Logs Applicatifs : docker compose logs -f odoo

    Redémarrage : docker compose restart odoo

    Arrêt Complet : docker compose down

    Mise à jour Config : docker compose up -d --force-recreate

7. Analyse de Rentabilité

    SaaS : Revenu récurrent par abonnement (50 000 - 150 000 FCFA/mois).

    Expertise : Prestations d'intégration et formation (300 000 - 500 000 FCFA).

    Optimisation : Coût infrastructure réduit (VPS) pour une marge brute estimée à 90%.

8. Validation par Captures d'écran

Le dossier Capture d'ecrans/ contient les validations suivantes :

    config_globale.png : Preuve du masquage de port et de la configuration du proxy système.

    conteneurs_actifs.png : Confirmation du statut opérationnel de tous les services Docker.

    interface_odoo.png : Preuve de l'accès final via HTTPS sécurisé sur le domaine dédié.

Auteur : INF3611 - Système et Réseaux | Université de Yaoundé 1 | 2025-2026