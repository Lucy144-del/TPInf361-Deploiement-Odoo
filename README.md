## Rapport de Déploiement INF3611

## Étudiant : DJOUTA WAMEGNE LUCIANA
## Matricule : 23V2283
## Application : Odoo (Business Suite)
## URL : https://23V2283.system-reso3

**1. Description de l'application**

Odoo est un Progiciel de Gestion Intégré (ERP) open-source utilisé par les entreprises pour centraliser leurs opérations. Il permet notamment la gestion des ventes, de la comptabilité, des stocks et des ressources humaines au sein d'une plateforme unique et modulaire.

Cas d'usage entreprise : Une PME peut remplacer ses 4-5 logiciels séparés (Excel, Sage, outils de facturation) par Odoo, centralisant toutes ses opérations avec automatisation des flux (commande → facture → stock → comptabilité).

## 2. Architecture des services

Le déploiement s'appuie sur une architecture multi-conteneurs orchestrée par Docker Compose :

    db : Serveur de base de données PostgreSQL (v15) dédié au stockage des données métier.

    odoo : Serveur d'application gérant la logique métier (Port exposé : 5880).

    nginx : Serveur Web utilisé comme Reverse Proxy pour la gestion du trafic entrant et la terminaison SSL/TLS.

    certbot : Service de génération et renouvellement automatique des certificats Let's Encrypt.

Schéma de communication :
text

[Utilisateur] HTTPS → [nginx:443] → [odoo:8069] ↔ [postgres:5432]

## 3. Structure du projet
text

```
odoo/
├── Capture d'ecrans            # dossier de capture d'ecran de l'interface odoo, en fonctionnement
│   ├── 1.png
│   ├── 2.png
│   └── 3.png
├── docker-compose.yml          # Définition des services
├── .env                        # Variables sensibles
├── nginx.conf                  # Configuration du proxy
└── README.md                   # Documentation
```

## 4. Configuration des variables d'environnement

Le fichier .env contient les configurations critiques pour la sécurité et le fonctionnement :

    DB_PASSWORD : Mot de passe d'accès à la base de données PostgreSQL.

    ODOO_ADMIN_PASSWORD : Mot de passe administrateur Odoo.

    DOMAIN : 23V2283.system-reso3 (sous-domaine personnel).

    CERTBOT_EMAIL : Email pour les notifications Let's Encrypt.

    Paramètres SMTP : Configuration complète pour l'envoi d'emails (serveur, port, utilisateur, mot de passe d'application).

Sécurité : Tous les secrets sont externalisés dans .env, fichier exclu du versioning.

## 5. Persistance des données

La persistance est assurée par des "bind mounts" pour garantir l'intégrité des données :

    ./postgres_data → /var/lib/postgresql/data (données PostgreSQL)

    ./odoo_app/config → /etc/odoo (configuration Odoo)

    ./odoo_app/addons → /mnt/extra-addons (modules supplémentaires)

    ./odoo_app/data → /var/lib/odoo (documents et fichiers)

Avantage : Les données survivent aux redémarrages et suppressions de conteneurs.

## 6. Sécurisation HTTPS

Le certificat TLS a été généré via Let's Encrypt avec Certbot intégré à Docker Compose.

Commande d'exécution :
bash

docker-compose run --rm certbot certonly \
  --webroot -w /var/www/certbot \
  --email luciana.djouta@facsciences-uy1.cm \
  -d 23V2283.system-reso3 \
  --agree-tos --non-interactive

Configuration NGINX :

    Redirection automatique HTTP → HTTPS

    Renouvellement automatique des certificats (cron intégré)

    Headers de sécurité (HSTS, X-Frame-Options, etc.)

## 7. Configuration SMTP

Pour les emails transactionnels (factures, réinitialisations de mots de passe) :

    Serveur : smtp.gmail.com:587 avec TLS

    Authentification : Mot de passe d'application Google (validation en 2 étapes requise)

    Test : Envoi d'email de test fonctionnel depuis l'interface Odoo

## 8. Instructions de déploiement
bash

## 1. Cloner et configurer
git clone <repository>
cd odoo-deployment
cp .env.example .env
# Éditer .env avec vos valeurs

## 2. Démarrer les services
docker-compose up -d

## 3. Générer le certificat SSL
docker-compose run --rm certbot

## 4. Accéder à l'application
## https://23V2283.system-reso3
## Utilisateur: admin | Mot de passe: [ODOO_ADMIN_PASSWORD]

## Commandes utiles :
bash

docker-compose logs -f odoo      # Voir les logs
docker-compose restart odoo      # Redémarrer Odoo
docker-compose run --rm certbot renew  # Renouveler certificat

## 9. Analyse de rentabilité (Business Case)
Modèles de monétisation :

    SaaS (Software as a Service)

        Abonnement mensuel : 50 000 - 150 000 FCFA/PME

        Inclut : hébergement, maintenance, sauvegardes, support

    Services d'intégration

        Installation sur mesure : 300 000 - 500 000 FCFA

        Migration depuis Excel/Sage : 200 000 - 400 000 FCFA

        Formation utilisateurs : 75 000 FCFA/jour/personne

    Développement sur mesure

        Modules personnalisés : 200 000 - 1 000 000 FCFA/module

        Interfaces mobiles : 500 000 - 1 500 000 FCFA

    Maintenance et support

        Forfait mensuel : 25 000 - 50 000 FCFA/mois

        Interventions ponctuelles : 15 000 FCFA/heure

Projection de revenus :

    10 clients SaaS à 75 000 FCFA/mois = 750 000 FCFA/mois

    2 installations/mois à 400 000 FCFA = 800 000 FCFA/mois

    4 formations/mois à 150 000 FCFA = 600 000 FCFA/mois

    Total mensuel potentiel : 2 150 000 FCFA

Coûts d'exploitation :

    VPS : 15 000 FCFA/mois

    Domaines : 2 000 FCFA/mois

    Marge brute : ~99%

## 10. Validation et tests

 HTTPS : Certificat valide, accès sécurisé
 Odoo : Interface accessible, base de données fonctionnelle
 SMTP : Emails envoyés avec succès
 Persistance : Données conservées après redémarrage
 Domaine : Accessible uniquement via https://23V2283.system-reso3

## Captures d'écran

Les captures d'écran de l'interface Odoo fonctionnelle sont disponibles dans le dossier `Capture d'ecrans/` 