## Rapport de Déploiement INF3611
## Étudiant : DJOUTA WAMEGNE LUCIANA
## Matricule : 23V2283
## Application : Odoo (Business Suite)
## URL : https://23v2283.systeme-res30.app

## 1. Description de l'application

    Odoo est un Progiciel de Gestion Intégré (ERP) open-source utilisé par les entreprises pour centraliser leurs opérations. Il permet notamment la gestion des ventes, de la comptabilité, des stocks et des ressources humaines au sein d'une plateforme unique et modulaire.

    * Cas d'usage entreprise : Une PME peut remplacer ses 4-5 logiciels séparés (Excel, Sage, outils de facturation) par Odoo, centralisant toutes ses opérations avec automatisation des flux (commande -> facture -> stock -> comptabilité).

## 2. Architecture des services

    Le déploiement s'appuie sur une architecture multi-conteneurs orchestrée par Docker Compose :

    * db      : Serveur de base de données PostgreSQL (v15) dédié au stockage des données métier.
    * odoo    : Serveur d'application gérant la logique métier (Port exposé : 5880).
    * nginx   : Serveur Web utilisé comme Reverse Proxy pour la gestion du trafic entrant et la terminaison SSL/TLS.
    * certbot : Service de génération et renouvellement automatique des certificats Let's Encrypt.

    Schéma de communication :
    [Utilisateur] HTTPS -> [nginx:443] -> [odoo:8069] <-> [postgres:5432]

## 3. Structure du projet

    odoo/
    ├── Capture d'ecrans/           # Dossier de captures d'écran (Interface, Config, Docker)
    │   ├── config_globale.png      # Preuve de la configuration Nginx système
    │   ├── conteneurs_actifs.png   # Preuve des services Up (docker compose ps)
    │   └── interface_odoo.png      # Preuve de l'interface Odoo fonctionnelle
    ├── docker-compose.yml          # Définition des services
    ├── .env.example                # Modèle des variables d'environnement
    ├── .env                        # Variables réelles (Exclu du versioning)
    ├── .gitignore                  # Fichiers et dossiers à ignorer par Git
    ├── nginx.conf                  # Configuration du proxy
    └── README.md                   # Documentation

## 4. Configuration des variables d'environnement

    Le fichier .env contient les configurations critiques pour la sécurité et le fonctionnement. 
    Un fichier .env.example est fourni pour guider la configuration :

    * DB_PASSWORD         : Mot de passe d'accès à la base de données PostgreSQL.
    * ODOO_ADMIN_PASSWORD : Mot de passe administrateur Odoo.
    * DOMAIN              : 23v2283.systeme-res30.app (sous-domaine personnel).
    * CERTBOT_EMAIL       : Email pour les notifications Let's Encrypt.
    * Paramètres SMTP     : Configuration complète pour l'envoi d'emails (Gmail App Password).

    * Sécurité : Tous les secrets sont externalisés dans .env, fichier exclu du versioning via .gitignore.

## 5. Persistance des données

    La persistance est assurée par des "bind mounts" pour garantir l'intégrité des données :

    * ./postgres_data  -> /var/lib/postgresql/data (données PostgreSQL)
    * ./odoo_app/config -> /etc/odoo (configuration Odoo)
    * ./odoo_app/addons -> /mnt/extra-addons (modules supplémentaires)
    * ./odoo_app/data   -> /var/lib/odoo (documents et fichiers)

    * Avantage : Les données survivent aux redémarrages et suppressions de conteneurs.

## 6. Sécurisation HTTPS

    Le certificat TLS a été généré via Let's Encrypt avec Certbot intégré à Docker Compose.

    Commande d'exécution :
    $ docker-compose run --rm certbot certonly --webroot -w /var/www/certbot ...

    Configuration NGINX :
    * Redirection automatique HTTP -> HTTPS.
    * Renouvellement automatique des certificats.
    * Masquage du port technique 5880 via le Nginx Global pour une URL propre.

## 7. Configuration SMTP

    Pour les emails transactionnels (factures, réinitialisations de mots de passe) :

    * Serveur : smtp.gmail.com:587 avec TLS.
    * Authentification : Mot de passe d'application Google (validation en 2 étapes requise).
    * Test : Envoi d'email de test fonctionnel depuis l'interface Odoo.

## 8. Instructions de déploiement

    ## . Cloner et configurer
        git clone <repository>
        cd odoo-deployment
        cp .env.example .env
        # Éditer le .env avec vos valeurs réelles

    ## . Démarrer les services
        docker-compose up -d

    ## . Commandes utiles
        docker-compose logs -f odoo      # Voir les logs
        docker-compose restart odoo      # Redémarrer Odoo
        docker-compose ps                # Vérifier l'état des services

## 9. Analyse de rentabilité (Business Case)

    Modèles de monétisation :
    * SaaS (Software as a Service) : Abonnement mensuel (50 000 - 150 000 FCFA/PME).
    * Services d'intégration : Installation sur mesure (300 000 - 500 000 FCFA).
    * Formation utilisateurs : 75 000 FCFA/jour/personne.
    * Maintenance et support : Forfait mensuel (25 000 - 50 000 FCFA/mois).

    * Projection de revenus : Potentiel de 2 150 000 FCFA/mois.
    * Coûts d'exploitation : VPS (15 000 FCFA/mois), Marge brute : ~99%.

## 10. Validation et tests

    * HTTPS      : Certificat valide, accès sécurisé sans port apparent.
    * Odoo       : Interface accessible, base de données fonctionnelle.
    * SMTP       : Emails envoyés avec succès.
    * Persistance: Données conservées après redémarrage.

## Captures d'écran

    Les preuves formelles du projet sont disponibles dans le dossier Capture d'ecrans/ :
    * config_globale.png    : Configuration du Nginx système pour l'accès propre.
    * conteneurs_actifs.png : Confirmation que tous les services sont opérationnels.
    * interface_odoo.png    : Capture d'Odoo fonctionnel en HTTPS sécurisé.