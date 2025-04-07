# AWS Security
## AWS KMS
AWS Key Management Service (**AWS KMS**) est un service managé qui permet de **créer, gérer et utiliser des clés cryptographiques** pour le chiffrement des données. KMS simplifie la gestion de la sécurité en centralisant le contrôle des clés tout en offrant une intégration transparente avec la majorité des services AWS.

- **Création et gestion centralisée des clés (CMK : Customer Managed Keys)**  
    - Génération automatique ou importation de clés externes  
    - Rotation automatique des clés gérées par AWS (AWS Managed Keys)  
    - Stockage sécurisé des clés (HSM, modules matériels certifiés FIPS 140-2 niveau 2)

- **Intégration native avec services AWS**  
    - Utilisé par : S3, EBS, RDS, DynamoDB, Lambda, Secrets Manager, etc.  
    - Simplifie le chiffrement/déchiffrement (ex : chiffrement transparent des données au repos sur EBS ou S3)

- **Chiffrement enveloppe (Envelope Encryption)**  
    - KMS génère une Data Key chiffrée avec la clé maîtresse (CMK)  
    - L’application utilise la Data Key pour chiffrer ses données, limitant l’usage direct de la clé principale et augmentant la sécurité et les performances

- **Politiques de clés (Resource-based policies)**  
    - Permet un contrôle granulaire sur qui peut utiliser ou gérer une clé spécifique  
    - Autorise facilement des accès inter-comptes ou limite l’usage de clés à certaines ressources ou utilisateurs spécifiques via IAM Policies

- **Audit et monitoring**  
    - Toutes les opérations de chiffrement/déchiffrement et gestion des clés sont journalisées dans AWS CloudTrail  
    - Facilité d’audit pour la conformité et la sécurité (PCI-DSS, GDPR, HIPAA)

- **Cas d’usage**  
    - Chiffrement des données sensibles stockées dans AWS (S3, EBS, RDS, etc.)  
    - Gestion centralisée des clés de chiffrement pour la conformité réglementaire  
    - Sécurité accrue des secrets et mots de passe (via Secrets Manager ou Parameter Store chiffrés avec KMS)

- **Pour l’examen AWS SAA**  
    - Très fréquent dans les questions sur le chiffrement, la sécurité des données, ou les exigences réglementaires  
    - Comprendre le principe du chiffrement enveloppe et l’importance de l’intégration avec les autres services AWS  
    - Connaître l’usage des politiques de clés pour le contrôle granulaire

## KMS Multi-Region Keys
Les **clés multi-régions** (Multi-Region Keys) dans **AWS KMS** permettent de répliquer automatiquement une clé de chiffrement sur plusieurs régions AWS. Cela simplifie la gestion du chiffrement dans des architectures multi-régionales tout en améliorant la résilience et la reprise après sinistre.

- **Gestion centralisée du chiffrement multi-région**
    - Une clé KMS primaire créée dans une région peut être automatiquement répliquée vers d’autres régions AWS.
    - Chaque région dispose alors d'une clé de réplication distincte, mais liée cryptographiquement à la clé primaire.

- **Avantages clés**
    - **Résilience accrue** : en cas de défaillance régionale, la clé est disponible ailleurs sans avoir à restaurer ou recréer manuellement.
    - **Simplification de la gestion** : pas besoin de créer et gérer indépendamment des clés séparées dans chaque région.
    - **Meilleure performance** : utilisation locale des clés pour réduire la latence des opérations de chiffrement/déchiffrement dans chaque région.

- **Cas d’usage typiques**
    - Applications multi-régions ou globales nécessitant le chiffrement cohérent des données (ex. Global Tables DynamoDB, S3 Cross-region Replication, Aurora Global Database).
    - Plans de reprise d’activité (Disaster Recovery) où une région secondaire doit accéder rapidement à des données chiffrées.

- **Utilisation et sécurité**
    - La gestion des permissions reste granulaire grâce aux politiques de clés individuelles par région.
    - Toutes les opérations restent auditées par CloudTrail, région par région.

- **Pour l’examen AWS SAA**
    - Peut être abordé dans les scénarios de chiffrement global, de haute disponibilité, et de disaster recovery.
    - Important à connaître pour les architectures multi-régionales ou globales, en particulier concernant la gestion simplifiée du chiffrement.


## S3 Replication with Encryption
- **Objets non chiffrés et objets chiffrés avec SSE-S3**  
    - **Répliqués automatiquement par défaut**, sans configuration supplémentaire  
    - Le chiffrement SSE-S3 est entièrement géré par AWS côté source et destination

- **Objets chiffrés avec SSE-C (Customer-provided keys)**  
    - **Réplicables**, mais uniquement si tu fournis manuellement la clé à chaque requête  
    - Peu recommandé car cela **complexifie fortement la gestion et la sécurité**  
    - Incompatible avec la réplication automatisée classique

- **Objets chiffrés avec SSE-KMS**  
    - **Ne sont pas répliqués par défaut**  
    - Il faut **activer explicitement** la réplication pour ces objets dans la configuration de la règle  
    - Tu dois **spécifier la clé KMS** à utiliser dans le bucket cible pour chiffrer les objets répliqués  
    - Il est nécessaire d’**adapter la Key Policy** de la clé KMS cible pour autoriser son utilisation par le rôle de réplication  
    - Le rôle IAM utilisé par la réplication doit avoir les permissions suivantes :  
        - `kms:Decrypt` sur la clé KMS **source**  
        - `kms:Encrypt` sur la clé KMS **cible**  
    - En cas de fort volume de réplication, tu peux rencontrer des **erreurs de limitation KMS (throttling)** → il faut alors **demander une augmentation de quota via Service Quotas**

- **Clés KMS multi-région**  
    - Tu peux les utiliser pour **simplifier la gestion entre régions**  
    - **Mais pour S3, elles sont considérées comme deux clés distinctes** (la clé source et la clé cible)  
    - Le processus de réplication consiste toujours à **déchiffrer avec la clé source** puis **re-chiffrer avec la clé cible**, même si c’est une multi-region key

- **Cas d’usage :**  
    - Réplication inter-région (CRR) avec chiffrement SSE-KMS pour assurer la **haute disponibilité** et la **conformité**  
    - Archivage sécurisé dans une autre région, tout en maintenant les **contrôles d’accès via KMS**  
    - Utilisation de clés KMS distinctes dans chaque région pour répondre à des politiques de sécurité strictes

- **Pour l’examen AWS SAA :**  
    - Savoir que **SSE-S3 est répliqué automatiquement**, **SSE-KMS nécessite une configuration spécifique**, et **SSE-C est à éviter** car non supporté par la réplication automatique  
    - Se souvenir que même avec des **multi-region KMS Keys**, S3 traite la réplication comme une opération de déchiffrement/re-chiffrement  
    - Identifier correctement les permissions KMS et IAM nécessaires (`kms:Decrypt`, `kms:Encrypt`)

## Encrypted AMI Sharing Process

- **L’AMI est chiffrée avec une clé KMS dans le compte source**  
    - Le chiffrement est effectué avec une **Customer Managed Key (CMK)** appartenant au **compte source**

- **Étape 1 : Ajouter une Launch Permission à l’AMI**  
    - Il faut **modifier les attributs de l’AMI** pour y ajouter un droit de lancement (`LaunchPermission`)  
    - Ce droit permet au **compte cible** d’utiliser l’AMI (mais pas encore les volumes associés)

- **Étape 2 : Partager les snapshots associés à l’AMI**  
    - Chaque volume de l’AMI est basé sur un **snapshot EBS chiffré**  
    - Ces snapshots doivent être **explicitement partagés** avec le **compte cible**

- **Étape 3 : Partager la clé KMS utilisée pour le chiffrement des snapshots**  
    - Le compte source doit modifier la **Key Policy** de la CMK pour :  
        - Donner accès au **compte cible ou à un rôle spécifique**  
        - Autoriser les actions suivantes :  
            - `kms:DescribeKey`  
            - `kms:ReEncrypt*`  
            - `kms:CreateGrant`  
            - `kms:Decrypt`  
    - Cela permet au compte cible de déchiffrer les snapshots et de lancer l’AMI

- **Étape 4 (optionnelle) : le compte cible peut re-chiffrer les volumes avec sa propre clé KMS**  
    - Lors du lancement de l’EC2 à partir de l’AMI, il est possible de **spécifier une autre clé KMS**, appartenant au compte cible  
    - Les volumes seront alors automatiquement **re-chiffrés avec cette nouvelle clé**

- **Résumé du flux de partage :**  
  1. Créer l’AMI chiffrée (avec une CMK du compte source)  
  2. Ajouter un **LaunchPermission** à l’AMI pour le compte cible  
  3. Partager les **snapshots** de l’AMI avec le compte cible  
  4. Modifier la **Key Policy KMS** pour autoriser le compte cible à utiliser la clé  
  5. (Facultatif) Le compte cible peut **re-chiffrer les volumes** avec sa propre CMK lors du lancement de l’instance

- **Cas d’usage :**  
    - Distribution contrôlée d’images EC2 chiffrées à d’autres comptes (filiales, partenaires, clients)  
    - Maintien des exigences de sécurité lors du partage de workloads sensibles

- **Pour l’examen AWS SAA :**  
    - Pas systématique mais peut apparaître dans des questions de **sécurité, inter-comptes et chiffrement avancé**  
    - À connaître : **un partage d’AMI chiffrée = partage de l’AMI + des snapshots + de la clé KMS**  
    - Important de retenir les permissions spécifiques KMS (`Decrypt`, `ReEncrypt`, etc.)

## SSM Parameter Store
- **Service de stockage sécurisé de paramètres de configuration et secrets**  
    - Permet de stocker des **paires clé/valeur** accessibles depuis d’autres services AWS ou ton code  
    - Paramètres lisibles dans **EC2, Lambda, ECS, scripts**, etc. via API, CLI ou SDK

- **Deux types de paramètres**  
    - **Standard Parameters** (gratuits, jusqu’à 4 Ko) : pour variables d’environnement, noms de ressources, chemins, etc.  
    - **SecureString Parameters** (chiffrés avec KMS) : pour stocker des **secrets sensibles** comme mots de passe, tokens, clés d’API  
        - Chiffrement possible avec la clé KMS par défaut ou une CMK personnalisée  
        - Accès contrôlé via IAM et audité via CloudTrail

- **Hiérarchie des paramètres**  
    - Organisation possible par chemins (`/app/env/db/password`)  
    - Facilite la gestion par environnement, service, ou application  
    - Les paramètres peuvent être référencés dynamiquement (`{{ssm:/path/to/param}}`)

- **Contrôle d’accès et sécurité**  
    - IAM permet de contrôler **qui peut lire/écrire/supprimer** un paramètre  
    - Pour les SecureStrings, l’accès au **chiffrement/déchiffrement est géré via KMS permissions** (`kms:Decrypt`)

- **Utilisation avec d'autres services AWS**  
    - **EC2** : injection de config à l’instance via User Data ou scripts  
    - **Lambda** : récupérer dynamiquement des secrets ou valeurs config au runtime  
    - **CodePipeline / CodeBuild** : utiliser les paramètres comme variables d’environnement  
    - **CloudFormation** : référencer des paramètres pour construire dynamiquement les stacks

- **Notifications et audit**  
    - Modifications et accès traçables via **AWS CloudTrail**  
    - Possibilité d’intégrer avec **EventBridge** pour réagir à des changements de valeur

- **Cas d’usage**  
    - Centraliser les **variables de configuration** multi-environnement (dev / prod)  
    - Gérer des **secrets ou credentials** sans les hardcoder dans les apps  
    - Rendre des stacks ou applications **plus portables, sécurisées et dynamiques**

- **Pour l’examen AWS SAA**  
    - Très fréquent pour les scénarios de **gestion de configuration, secrets, et bonnes pratiques de sécurité**  
    - Bien connaître la différence entre **Standard** et **SecureString**, et leur intégration avec **IAM + KMS**

## AWS Secret Manager

- **Service managé pour stocker, gérer et faire la rotation automatique des secrets**  
    - Conçu pour **gérer des informations sensibles** : mots de passe, clés API, tokens, credentials de base de données, etc.  
    - Évite de stocker des secrets en clair dans le code ou dans des fichiers de config  

- **Chiffrement natif avec AWS KMS**  
    - Tous les secrets sont **chiffrés au repos** avec une **clé KMS (par défaut ou personnalisée)**  
    - L’accès aux secrets chiffrés est contrôlé par **IAM** et les autorisations **KMS (ex : `kms:Decrypt`)**

- **Rotation automatique des secrets**  
    - Intégré nativement avec **RDS, Redshift, DocumentDB**, et d’autres services AWS  
    - Possibilité de **définir une rotation personnalisée** via une fonction **AWS Lambda**  
    - Rotation basée sur un calendrier (ex : tous les 30 jours) ou manuellement

- **Récupération sécurisée à la demande**  
    - Les applications peuvent appeler l’API Secrets Manager pour récupérer un secret de manière sécurisée  
    - L’accès est journalisé via **AWS CloudTrail**

- **Intégration avec d'autres services AWS**  
    - **Lambda** : lecture d’un secret au runtime via SDK  
    - **RDS** : connexion à la base de données avec des credentials automatiquement gérés  
    - **ECS, EC2, CodeBuild, etc.** : accès sécurisé aux secrets via variables d’environnement

- **Différences avec SSM Parameter Store**  
    - Secrets Manager est **optimisé pour les secrets sensibles avec rotation automatique**  
    - SSM Parameter Store est plus **généraliste** (configurations, variables, secrets simples)  
    - Secrets Manager a un **coût par secret stocké**, tandis que SSM a un niveau gratuit pour les Standard Parameters

- **Cas d’usage**  
    - Stocker des **identifiants de base de données** et les faire tourner automatiquement  
    - **Remplacer les fichiers `.env` ou les credentials codés en dur**  
    - Intégrer la sécurité des secrets dans un pipeline CI/CD ou une stack DevOps  
    - Gérer les clés API tierces utilisées dans une application cloud

- **Pour l’examen AWS SAA**  
    - Très fréquent dans les scénarios de **sécurité, chiffrement, gestion de secrets dynamiques**  
    - Retenir que **Secrets Manager = rotation automatique + chiffrement + audit + intégration Lambda**  
    - Bien différencier de **SSM Parameter Store** en fonction du besoin (secrets sensibles VS config)

## AWS Certificate Manager
- **Service managé pour gérer les certificats SSL/TLS publics et privés**  
    - Facilite le **provisionnement, déploiement et renouvellement** des certificats pour les services AWS (ALB, CloudFront, API Gateway, etc.)  
    - Permet de **générer un certificat via ACM** ou **d’importer un certificat tiers**

- **Création d’un certificat public via ACM**  
    - Tu dois **spécifier les noms de domaine** à inclure dans le certificat :  
        - **Nom de domaine complet (FQDN)** : ex. `corp.example.com`  
        - **Domaine générique (Wildcard)** : ex. `*.example.com`  
    - Deux méthodes de validation sont disponibles :  
        - **DNS Validation (recommandée)**  
            - ACM te fournit un enregistrement **CNAME** à ajouter dans la zone DNS (ex : Route 53)  
            - Idéal pour les environnements automatisés  
        - **Email Validation**  
            - Envoie des emails aux contacts du domaine listés dans le **WHOIS**  
            - Moins pratique pour les infrastructures automatisées  
    - La validation peut prendre **quelques heures**  
    - Une fois validé, le certificat est **inscrit au renouvellement automatique**  
    - ACM **renouvelle automatiquement les certificats 60 jours** avant expiration, sans intervention  
    - Le certificat **n’est utilisable que sur les services AWS intégrés à ACM** (pas de téléchargement)

- **Option d’importer un certificat externe**  
    - Possibilité d’importer un certificat généré ailleurs (ex : Let's Encrypt, DigiCert, etc.)  
    - **Pas de renouvellement automatique** → il faut **réimporter manuellement** le certificat avant son expiration  
    - ACM envoie des **événements d’expiration quotidiens** à partir de **45 jours avant la date limite** (configurable)  
        - Ces événements sont disponibles dans **EventBridge**  
        - **AWS Config** propose une **règle managée `acm-certificate-expiration-check`** pour détecter les certificats proches de l’expiration

- **Cas d’usage**  
    - Sécuriser l’accès à un site web via HTTPS sur un **Load Balancer ou CloudFront**  
    - Fournir une **API sécurisée via API Gateway**  
    - Centraliser la gestion des certificats dans un environnement **multi-domaines / multi-applications**  
    - Automatiser la **validation DNS via Route 53** pour faciliter les déploiements

- **Pour l’examen AWS SAA**  
    - Savoir que **les certificats publics ACM sont gratuits, auto-renouvelés et utilisables uniquement dans AWS**  
    - Bien faire la distinction entre **certificats ACM générés** (auto-renouvelés) et **certificats importés** (manuels)  
    - Retenir les **méthodes de validation (DNS vs Email)** et l’intégration avec **Route 53 / EventBridge / AWS Config**

## AWS Web Application Firewall
- **Service managé de protection des applications web contre les attaques**  
    - Protège contre les menaces courantes : **injections SQL**, **scripts intersites (XSS)**, **bots**, **tentatives de contournement d’URL**, **DDoS applicatifs**, etc.  
    - Fonctionne au niveau **L7 (HTTP/HTTPS)** du modèle OSI

- **Fonctionne avec plusieurs services AWS**  
    - Peut être associé à **Amazon CloudFront**, **Application Load Balancer (ALB)**, **API Gateway**, **AWS App Runner**  
    - Filtre le trafic **avant qu’il n’atteigne ton application**

- **Web ACL (Access Control List)**  
    - Ensemble de **règles** appliquées à une ressource (CloudFront, ALB, etc.)  
    - Une Web ACL contient une ou plusieurs **Rules** ou **Rule groups** (groupes de règles)  
    - Chaque règle définit une **condition** (ex : correspondance d’un User-Agent, détection de requêtes anormales…) et une **action** (`Allow`, `Block`, `Count`)  

- **Règles personnalisées ou gérées par AWS**  
    - Tu peux créer des règles **sur mesure** avec des conditions spécifiques :  
        - IP Set, Geo Match, Header Match, Rate-based rules, etc.  
    - AWS propose aussi des **Managed Rule Groups** :  
        - Maintenus par AWS ou ses partenaires  
        - Prêts à l’emploi pour bloquer des attaques courantes (ex : OWASP Top 10, bots, scanners connus)  

- **Fonctionnalités avancées**  
    - **Rate-based rules** : limite le nombre de requêtes par IP sur une période donnée  
    - **Bot Control** : identifie et filtre les bots (avec visibilité et gestion fine)  
    - **Captcha / Challenge** : ajouter un challenge avant d’autoriser l’accès (ex : pour les bots ou utilisateurs suspects)  
    - **Labeling & Logic chaining** : ajouter des étiquettes aux requêtes pour chaîner des règles conditionnelles complexes  
    - **Logging & Monitoring** : journalisation via **Kinesis Firehose**, analyse dans **CloudWatch**, **S3**, ou **OpenSearch**

- Cas d’usage  
    - Protéger un site web public contre les attaques classiques du web (injections, XSS, bots)  
    - Ajouter une couche de sécurité devant une API Gateway  
    - Bloquer l’accès à certains pays ou IP connues  
    - Prévenir les abus (ex : limitation du taux de requêtes par IP)

- Pour l’examen AWS SAA  
    - AWS WAF est souvent mentionné dans les scénarios de **sécurité au niveau applicatif**  
    - Bien connaître les **ressources compatibles (CloudFront, ALB, API Gateway)**  
    - Savoir que les **Managed Rules simplifient la mise en place de protection sans expertise approfondie**  
    - Savoir que WAF agit au niveau **Layer 7** et se configure via **Web ACL + Rules**

## AWS Shield
- **Service managé de protection contre les attaques DDoS (Distributed Denial of Service)**  
    - Protège automatiquement les applications hébergées sur AWS contre les attaques réseau de **volumétrie**, de **flood UDP/TCP**, ou de **tentatives d’épuisement applicatif**  
    - Conçu pour **minimiser les interruptions de service** dues à des attaques

- **Deux niveaux de service**  
    - **AWS Shield Standard (par défaut, gratuit)**  
        - Activé automatiquement sur tous les services AWS en front (CloudFront, ALB, API Gateway, Route 53…)  
        - Protège contre les attaques DDoS **les plus courantes** (ex : SYN flood, DNS query flood, reflection, etc.)  
        - Aucune configuration requise, protection toujours active  
        - Utilise **AWS Global Edge Network** pour absorber les attaques proches de la source

    - **AWS Shield Advanced (payant)**  
        - Fournit une protection **plus approfondie et personnalisée**, avec :  
            - **Détection d’anomalies** plus fines (L3, L4, L7)  
            - **Visibilité en temps réel** et alertes détaillées via CloudWatch + EventBridge  
            - **Support 24/7 d’experts DDoS (DDoS Response Team - DRT)**  
            - **Plans de remédiation** personnalisés  
            - **Protection financière (DDoS cost protection)** : remboursement des frais liés à une montée en charge causée par une attaque  
        - Recommandé pour les applications critiques ou fortement exposées

- **Intégration avec d’autres services AWS**  
    - Fonctionne conjointement avec **AWS WAF**, **CloudFront**, **Route 53**, **ALB**, etc.  
    - Peut déclencher des **règles automatisées** via EventBridge ou Lambda lors d'une attaque détectée  
    - Centralisation de la protection avec **AWS Firewall Manager** pour les grands environnements multi-comptes

- **Cas d’usage**  
    - Protéger des **applications web publiques** contre les attaques volumétriques ou ciblées  
    - Offrir une **garantie de disponibilité** pour un site de e-commerce ou une API critique  
    - Coupler avec WAF pour **défense multi-niveaux : DDoS + application layer (L7)**  

- Pour l’examen AWS SAA  
    - Savoir que **Shield Standard est activé automatiquement et gratuitement**  
    - Savoir que **Shield Advanced est payant**, fournit un support DDoS expert + protection financière  
    - Comprendre que Shield est **orienté réseau (L3/L4)** tandis que WAF protège **le niveau applicatif (L7)**  
    - Shield Advanced peut apparaître dans les scénarios **de haute disponibilité, sécurité renforcée ou conformité**

## AWS Firewall Manager
- **Service managé pour centraliser la gestion des règles de sécurité réseau et applicatives**  
    - Permet de **déployer et gérer depuis un seul endroit** les configurations de sécurité sur **plusieurs comptes AWS et régions**  
    - Fonctionne avec **AWS Organizations** pour appliquer des politiques à l’ensemble d’une organisation

- **Centralisation des règles de sécurité sur différents services**  
    - **AWS WAF** : déploiement de **Web ACLs** (règles anti-bots, XSS, SQLi, etc.) sur ALB, API Gateway, CloudFront…  
    - **AWS Shield Advanced** : gestion centralisée de l’abonnement, détection et réponse DDoS pour toutes les ressources  
    - **VPC Security Groups** : détection de **règles non conformes**, possibilité d’auditer et de corriger automatiquement  
    - **AWS Network Firewall** : gestion des politiques de pare-feu réseau dans des VPC  
    - **Route 53 Resolver DNS Firewall** : centralise les règles DNS de filtrage (blocage de domaines malveillants)

- **Politiques de sécurité définies par l’administrateur**  
    - Application automatique sur tous les comptes cibles d’une **OU (Organizational Unit)** ou de l’organisation entière  
    - Peut imposer une **conformité stricte** :  
        - Les comptes ne peuvent pas modifier les règles appliquées  
        - Les nouvelles ressources créées sont automatiquement protégées  

- **Détection de non-conformité**  
    - Fournit une **vue centralisée** des ressources non protégées ou mal configurées  
    - Peut déclencher des **actions automatiques de correction** ou simplement signaler les écarts (mode audit)  
    - Notifications via **CloudWatch, EventBridge** ou AWS Config

- Cas d’usage  
    - Protéger toutes les applications publiques d’un grand groupe avec **les mêmes règles WAF**  
    - Déployer **Shield Advanced** sur tous les comptes prod automatiquement  
    - Auditer en continu les **Security Groups** pour détecter les règles trop permissives  
    - Appliquer un filtrage DNS d’entreprise à tous les VPC via Route 53 Resolver DNS Firewall

- Pour l’examen AWS SAA  
    - À connaître si tu vois passer une **architecture multi-comptes avec des besoins de sécurité cohérents**  
    - Savoir que **Firewall Manager = gestion centralisée des règles de sécurité (WAF, Shield, SG, Network Firewall)**  
    - Doit être couplé à **AWS Organizations**, avec un **compte administrateur désigné**

## Amazon GuardDuty

- **Service de détection de menaces managé basé sur l’analyse intelligente des données AWS**  
    - Permet de **détecter automatiquement des comportements malveillants ou non autorisés** dans ton environnement AWS  
    - Aucune infrastructure à déployer, **activation en un clic** par région et par compte

- **Analyse de sources de données de sécurité AWS**  
    - **AWS CloudTrail** : surveille les appels d’API suspects (ex : `CreateUser` depuis une IP inconnue)  
    - **VPC Flow Logs** : détecte du trafic réseau anormal (ex : connexions sortantes vers un botnet)  
    - **DNS Logs** : identifie des résolutions de noms vers des domaines malveillants ou exfiltration de données  
    - **EKS Audit Logs** (optionnel) : surveille les activités suspectes dans tes clusters Kubernetes managés  
    - **S3 Data Events** (optionnel) : détecte des lectures anormales ou des accès non autorisés aux buckets

- **Détection basée sur le machine learning, l’intelligence AWS et des bases de menaces (threat intelligence)**  
    - Utilise les données de **AWS Security**, de **partenaires tiers (ex : Proofpoint, Crowdstrike)** et ses propres algorithmes  
    - Classe les événements détectés en **trois niveaux de sévérité** : Low, Medium, High  
    - Exemples de détections :  
        - IAM utilisé depuis une **IP étrangère** ou **impossible (geolocation impossible)**  
        - **Port scanning** interne  
        - Exécution inhabituelle de commandes AWS  
        - Accès anormal à des données S3

- **Intégration avec d’autres services AWS**  
      - Envoie les findings (alertes) vers **CloudWatch Events / EventBridge** → permet des actions automatiques (ex : blocage, isolation)  
      - Peut être relié à **AWS Security Hub** pour centraliser les alertes  
      - Peut déclencher des **remédiations automatiques via Lambda**, SSM ou des playbooks Step Functions

- **Multi-comptes / Multi-régions avec AWS Organizations**  
      - Possibilité de désigner un **compte maître GuardDuty** pour gérer et agréger les findings de tous les comptes membres  
      - Permet une **vue sécurité unifiée à l’échelle de l’organisation**

- **Cas d’usage**  
      - Détecter et **isoler une instance EC2 compromise** qui contacte un serveur C2  
      - Identifier un **compte IAM détourné** ou utilisé depuis un pays jamais observé  
      - Surveiller un **bucket S3 sensible** pour détecter une lecture massive ou suspecte  
      - Protéger une **infrastructure Kubernetes (EKS)** contre les abus ou accès non conformes

- **Pour l’examen AWS SAA**
      - Important dans les questions de **surveillance, sécurité proactive et détection de menaces**  
      - À connaître comme une solution **non intrusive** (pas d’agent à installer), **automatique** et **centrale** pour la détection  
      - Bien comprendre la **différence entre GuardDuty (détection), Config (conformité), CloudTrail (traçage) et WAF/Shield (protection)**

## Amazon Inspector
- **Service managé d’analyse automatisée de sécurité des ressources AWS**  
      - Permet d’**identifier automatiquement les vulnérabilités connues (CVEs)** et **mauvaises configurations** dans les environnements **EC2**, **ECS**, et **Lambda**  
      - Fonctionne en continu, sans intervention manuelle une fois activé

- **Analyse de sécurité automatisée avec détection des failles**  
      - **Scan en temps quasi réel** dès qu’une nouvelle image ou instance est détectée  
      - Repose sur une base de données de vulnérabilités (CVEs) régulièrement mise à jour  
      - Priorise les findings selon leur **niveau de sévérité** et le **contexte d’exploitation réel** (score CVSS, présence de processus en exécution, exposition réseau…)

- **Ressources analysées automatiquement**  
      - **Instances EC2** : analyse du système d’exploitation et des packages installés  
      - **Images Amazon ECR (Elastic Container Registry)** : détection de failles dans les images de conteneurs dès leur push  
      - **Fonctions Lambda** : vérification du code et des dépendances packagées  
      - **ECS (Fargate)** : prise en charge via analyse des images ECR utilisées

- **Fonctionnement sans agent (Inspector v2)**  
      - Utilise **AWS Systems Manager (SSM Agent)** déjà présent sur EC2 (ou installé automatiquement)  
      - Aucun logiciel à installer pour ECR ou Lambda : l’analyse se fait côté service  
      - Active les scans via la console ou CLI (activation régionale)

- **Rapports de findings (alertes)**  
      - Chaque vulnérabilité identifiée est listée avec :  
            - Son **identifiant CVE**, **score CVSS**, **niveau de risque**, et **chemin affecté**  
            - Des **recommandations de correction** (mise à jour package, patch OS, etc.)  
      - Résultats visibles dans la console Amazon Inspector, et exportables via **SNS / EventBridge**

- **Intégration avec d’autres services AWS**  
      - Envoi automatique des findings vers **AWS Security Hub**, **EventBridge**, ou vers un système de ticketing via Lambda  
      - **Tagging possible** pour filtrer quelles ressources doivent être scannées ou non  
      - Compatible avec **AWS Organizations** pour une gestion multi-comptes centralisée

- **Cas d’usage**  
      - Scanner automatiquement tous les conteneurs stockés dans ECR pour vérifier leur sécurité avant déploiement  
      - Détecter des **EC2 vulnérables** ou exposées avec des ports ouverts + failles critiques  
      - **Automatiser la sécurité dans les pipelines CI/CD** en bloquant le déploiement d’images contenant des vulnérabilités critiques  
      - Obtenir une **vue centralisée des risques de sécurité** dans un environnement AWS

- **Pour l’examen AWS SAA** 
      - Moins fréquent, mais peut apparaître dans les questions de **vulnérabilité, analyse de sécurité, ou DevSecOps**  
      - Bien comprendre qu’Inspector analyse : **EC2, ECR, Lambda, ECS**, et que **v2 ne nécessite pas d’agent spécifique**  
      - À différencier de GuardDuty (menaces comportementales), Inspector se concentre sur les **vulnérabilités techniques** connues (CVE)

## Amazon Macie

- **Service managé d’analyse de sécurité basé sur l’intelligence artificielle**  
    - Permet d’**identifier, classifier et protéger les données sensibles** stockées dans **Amazon S3**  
    - Utilise des techniques de **machine learning** pour reconnaître automatiquement des données comme :  
        - **Informations personnelles (PII)**  
        - **Informations financières (ex : numéros de carte bancaire)**  
        - **Informations médicales (PHI)**  
        - **Identifiants d’accès** (clés AWS, tokens API…)

- **Scan automatique des buckets S3**  
    - Analyse le contenu des objets stockés dans S3 pour **détecter les données sensibles**  
    - Peut analyser tous les buckets, ou seulement certains selon des critères (tags, noms, régions…)  
    - Fournit un rapport avec :  
        - Le **type de données détecté**  
        - Le **niveau de sensibilité**  
        - L’**emplacement** exact (bucket, objet, ligne)

- **Fonctionnalités clés**  
    - **Inventory des buckets S3** : recense tous les buckets avec leur statut de chiffrement, d’accessibilité publique, de versioning…  
    - **Alertes en cas de risque** : accès public d’un bucket contenant des données sensibles, ou détection d’un grand volume d’infos personnelles  
    - **Résultats exportables** vers **EventBridge**, **CloudWatch**, ou un SIEM via **Kinesis Data Firehose**

- **Intégration avec d’autres services AWS**  
    - Compatible avec **Security Hub** pour centraliser les findings  
    - Peut être couplé à **Lambda ou Step Functions** pour automatiser les réponses (ex : mettre un bucket privé, alerter une équipe)  
    - Multi-comptes possible via **AWS Organizations** pour un pilotage global de la détection de données sensibles

- Cas d’usage  
    - **Audit de conformité** : vérifier que les buckets ne contiennent pas d’infos sensibles non chiffrées ou exposées  
    - **RGPD, HIPAA, PCI-DSS** : identifier les données personnelles et confidentielles pour répondre aux obligations réglementaires  
    - **Réduction de la surface d’exposition** : détecter et corriger les buckets publics contenant des données critiques  
    - **Surveillance continue** des dépôts de données dans le cloud

- Pour l’examen AWS SAA  
    - Peut apparaître dans les contextes de **sécurité des données, conformité, ou gouvernance des S3**  
    - Bien comprendre que **Macie = détection automatique de données sensibles dans S3 uniquement**  
    - À différencier de GuardDuty (menaces comportementales) ou Inspector (vulnérabilités techniques)
