# IAM & Access Management
## IAM
- **Policies** : Pour Users et Groupes
- **Roles** : Pour Services

## Organizations
**AWS Organizations** est un service qui te permet de **centraliser la gestion de plusieurs comptes AWS** au sein d’un même environnement structuré et sécurisé. Il est particulièrement utile dans les environnements multi-comptes pour **contrôler l’accès, standardiser les politiques**, et **regrouper la facturation**.

- **Gestion centralisée de plusieurs comptes AWS**  
	  - Création de **comptes enfants** depuis un compte "maître" (root account de l’organisation)  
	  - Possibilité d’ajouter des **comptes existants** à une organisation  
	  - Accès au **tableau de bord global** pour voir l’état et la structure  

- **Unités organisationnelles (OUs)**  
	  - Permet de **regrouper des comptes par fonction** : ex. "Prod", "Dev", "Sécurité", etc.  
	  - Application de politiques **par OU ou par compte** pour faciliter la gouvernance  
	  - Structure hiérarchique logique, très utile pour les grands environnements  

- **Service Control Policies (SCPs)**  
	  - Politiques globales de contrôle d’accès sur les comptes  
	  - Permettent de **restreindre les permissions**, même si l’utilisateur IAM dispose des droits dans son compte  
	  - Exemples : interdire la suppression de CloudTrail, restreindre l’usage de certains services, obliger à utiliser une région spécifique  

- **Consolidated Billing (facturation consolidée)**  
	  - Tous les comptes d’une organisation **partagent la même facture**  
	  - Possibilité de voir la consommation détaillée **par compte**  
	  - Accès à des **remises sur volume** globalement partagées entre les comptes

- **Intégration avec d’autres services AWS**  
	  - AWS Config, CloudTrail, GuardDuty, Security Hub, etc. peuvent être **centralisés** au niveau de l’organisation  
	  - Permet un **compte de sécurité dédié** pour regrouper tous les logs, alertes, audits  

- **Cas d’usage** :  
	  - **Structure multi-comptes** : séparation claire entre dev/test/prod pour la sécurité et la gestion.  
	  - **Gouvernance et conformité** : restreindre les actions globalement via SCPs.  
	  - **Optimisation des coûts** : remises sur volume + suivi précis par compte.  
	  - **Centralisation des audits** : regrouper tous les logs (CloudTrail, Config, etc.) dans un compte "sécurité".

- **Pour l’examen AWS SAA** :  
	  - Savoir qu’**Organizations** est la solution pour **gérer plusieurs comptes AWS**.  
	  - Comprendre le rôle des **SCPs**, **OUs**, et **facturation consolidée**.  
	  - On peut t’interroger sur **la propagation des politiques SCPs**, ou sur comment **limiter les droits sur tous les comptes**, même avec des permissions IAM.

## IAM - Advanced Policies
- **IAM Conditions**  
	  - Permettent d’appliquer une **logique conditionnelle** dans les politiques IAM (Identity-based policies).  
	  - Utilisées pour **restreindre les permissions** selon le contexte (adresse IP, utilisateur, tag, organisation…).  
	  - Exemple : `Condition: {"IpAddress": {"aws:SourceIp": "192.168.0.1/24"}}` → n’autorise l’accès que depuis un certain bloc IP  
	  - Autres opérateurs : `StringEquals`, `Bool`, `ArnLike`, `DateGreaterThan`, etc.  
	  - Couramment utilisées avec : `aws:RequestTag`, `aws:TagKeys`, `aws:sourceVpc`, `aws:username`, etc.

- **IAM pour S3 (politiques IAM + politiques de bucket)**  
	  - **IAM policies** (attachées aux users/roles) peuvent accorder des droits sur des actions S3 (`s3:GetObject`, `s3:PutObject`, etc.).  
	  - Pour un contrôle plus précis côté **ressource**, on utilise aussi les **Bucket Policies** (resource-based) :  
		    - Exemple : limiter l’accès à certains préfixes ou objets.  
		    - Peut être combiné avec des **conditions** (`aws:username`, `aws:sourceVpc`, etc.).  
	  - **IAM ne suffit pas toujours** : les buckets publics, les accès inter-comptes ou la gestion avancée des accès exigent des politiques de bucket ou d’accès point (Access Points).

- **Resource Policies**  
	  - Politiques **attachées à la ressource elle-même** (pas à un utilisateur IAM)  
	  - Utilisées notamment dans S3, SNS, SQS, Lambda, API Gateway, KMS…  
	  - Permettent de **donner l’accès à des comptes tiers ou à des services** AWS  
	  - Peuvent inclure des **conditions IAM classiques** (IP, VPC, tag, etc.)  
	  - Exemple pour S3 : autoriser l’accès à un autre compte AWS en ajoutant son ARN dans la ressource

- **`aws:PrincipalOrgID`**  
	  - Condition spéciale pour **limiter l’accès à une ressource aux comptes faisant partie de la même AWS Organization**  
	  - Très utile pour sécuriser l’accès **entre comptes membres d’une même orga**, sans exposer la ressource publiquement  
	  - Exemple :  
	    ```json
	    "Condition": {
	      "StringEquals": {
	        "aws:PrincipalOrgID": "o-xxxxxxxxxx"
	      }
	    }
	    ```  
	  - Fonctionne bien avec **S3 Bucket Policies**, **KMS**, **SQS**, **Lambda**, **Secrets Manager**, etc.  
	  - Requiert que l’appelant ait un **principal AWS valide**, ex : rôle IAM dans un autre compte

- **Cas d’usage** :  
	  - **IAM Conditions** : restreindre des actions à certaines plages IP, à certains VPCs ou utilisateurs.  
	  - **IAM pour S3** : contrôler l’accès des utilisateurs à des buckets/objets spécifiques, avec des préfixes ou tags.  
	  - **Resource Policies** : accorder l’accès à des services ou comptes externes en mode contrôlé.  
	  - **aws:PrincipalOrgID** : sécuriser les accès inter-comptes dans une organisation sans exposer publiquement.

- **Pour l’examen AWS SAA** :  
	  - Connaître les différences entre **IAM policies**, **Resource policies** et **Bucket Policies**.  
	  - Savoir que `aws:PrincipalOrgID` permet de restreindre à l’organisation AWS (important en multi-compte).  
	  - Retenir que **IAM Conditions** sont souvent combinées pour restreindre encore plus précisément l’usage des permissions.  

## IAM Roles vs Resource Based Policies
- **IAM Roles**  
	  - Entités IAM **associées à des permissions** (comme les IAM users) mais **sans identifiants permanents**  
	  - S’utilisent via **assume role** : une autre entité (user, service AWS, compte) **endosse** ce rôle pour exécuter des actions  
	  - **Attachées à des identités**, pas à des ressources  
	  - Types d’usage :  
		    - **Rôles pour services AWS** (ex : EC2 qui doit lire un bucket S3)  
		    - **Rôles inter-comptes** (un compte A assume un rôle dans un compte B)  
		    - **Rôles temporaires via STS** (ex : accès temporaire à un tiers)  
	  - Les permissions sont **définies dans une policy IAM** attachée au rôle  
	  - L’action est autorisée si la **trust policy** autorise l’entité à assumer le rôle  
	  - Exemples :  
		    - EC2 assume un rôle pour écrire dans CloudWatch  
		    - Un utilisateur assume un rôle "admin temporaire" pour effectuer une tâche sensible

- **Resource-based Policies**  
	  - Politiques **attachées directement à une ressource AWS** (pas à un rôle ou un utilisateur)  
	  - Permettent de **donner l’accès à une ressource à une autre entité AWS**, y compris à des **comptes externes**  
	  - Contiennent une clause `"Principal"` qui **désigne qui peut accéder** à la ressource  
	  - Types de ressources supportant les resource-based policies :  
		    - **S3** (Bucket Policy)  
		    - **SQS**, **SNS**  
		    - **Lambda**, **Secrets Manager**, **KMS**, **API Gateway**, **EventBridge**, etc.  
	  - Possibilité de filtrer avec des **conditions** (`aws:SourceIp`, `aws:PrincipalOrgID`, `aws:SourceArn`, etc.)  
	  - Exemples :  
		    - Donner accès à un **autre compte** pour consommer une queue SQS  
		    - Donner accès à un **autre rôle Lambda** pour invoquer une fonction  
		    - Permettre à un service (ex : CloudWatch Events) d’invoquer une Lambda  

- **Cas d’usage** :  
	  - IAM Role → **je veux qu’un service ou une personne puisse "endosser un rôle"** pour accéder à quelque chose  
	  - Resource Policy → **je veux qu’une ressource soit accessible à certains rôles ou comptes définis**

- **Pour l’examen AWS SAA** :  
	  - Savoir que les **IAM Roles** sont **assumés par une entité** (et nécessitent une "trust policy")  
	  - Les **Resource Policies** sont **attachées aux ressources** et contiennent une section `"Principal"`  
	  - Très courant dans les questions sur **accès inter-comptes**, **accès croisé de services**, ou **sécurité S3 / Lambda**

## IAM Permission Boundaries

- **Définissent une limite maximale de permissions** qu’un **rôle ou utilisateur IAM peut utiliser**, même s’il a des policies attachées plus permissives  
	  - C’est une **barrière de sécurité** : un utilisateur ne peut exercer **que l’intersection** entre ses permissions IAM **et** la **Permission Boundary**  
	  - On parle souvent de **"maximum allowable permissions"**

- **Utilisées principalement dans les environnements où les utilisateurs ou rôles peuvent créer d'autres rôles / utilisateurs**  
	  - Exemple : une équipe DevOps peut créer des rôles IAM pour ses apps, mais on veut s’assurer qu’ils **ne donnent pas trop de pouvoir** à ces rôles  
	  - Permet d’**encadrer la délégation d’autorisations** dans des environnements multi-équipes ou multi-comptes

- **Fonctionnement logique**  
	  - Lorsqu’un utilisateur IAM fait une action, AWS vérifie :  
		    - La **policy IAM attachée** au user ou rôle  
		    - La **permission boundary**, si elle existe  
		    - L’**intersection** des deux doit autoriser l’action  
	  - Si l’une des deux **refuse ou ne mentionne pas** l’action → l’accès est refusé

- **Permission boundaries ne remplacent pas les IAM policies classiques**  
	  - Elles **complètent** les IAM policies, mais **ne suffisent jamais à elles seules**  
	  - L'utilisateur ou rôle **doit avoir une permission IAM valide** + cette permission doit aussi être **autorisée par la boundary**

- **Cas d’usage** :  
	  - **Encadrer les droits des rôles créés par un utilisateur IAM**  
	  - **Limiter la portée** des droits temporaires donnés via STS ou AssumeRole  
	  - **Systèmes automatisés** de création de rôles (ex: CloudFormation, Terraform) avec garde-fous  

- **Pour l’examen AWS SAA** :  
	  - **Moins fréquent**, mais peut apparaître dans des questions de **contrôle d’accès fin** ou de **sécurité multi-équipes**  
	  - Savoir que c’est un **mécanisme pour limiter ce qu’un rôle/user peut faire, même avec une policy IAM permissive**  
	  - À ne pas confondre avec **SCPs (Service Control Policies)** d’AWS Organizations (qui limitent les comptes entiers)

## AWS IAM Identity Center
AWS IAM Identity Center (anciennement **AWS SSO**) est un service qui centralise la gestion des identités et des accès dans un environnement multi-comptes AWS. Il permet aux utilisateurs d’accéder à tous leurs comptes AWS, applications cloud (SaaS) et systèmes internes via un portail unique (Single Sign-On).

- **Gestion centralisée des utilisateurs et des groupes**
	  - Centralise la création et la gestion des identités des utilisateurs
	  - Supporte les annuaires externes (Active Directory, Okta, Azure AD, etc.)
	  - Permet de gérer facilement qui a accès à quoi, depuis un seul endroit

- **Single Sign-On (SSO)**
	  - Fournit un portail web centralisé où les utilisateurs se connectent une seule fois
	  - Accès simplifié aux comptes AWS (console, CLI, SDK) et aux applications SaaS compatibles (Salesforce, Slack, Office 365, etc.)
	  - Réduit le nombre d’identifiants à gérer, améliorant ainsi la sécurité

- **Intégration avec AWS Organizations**
	  - Distribue automatiquement les accès aux multiples comptes AWS à partir d’une gestion centralisée
	  - Simplifie la gestion multi-comptes, avec une visibilité claire des autorisations accordées

- **Contrôle granulaire des accès**
	  - Définition de permission sets, qui sont des ensembles prédéfinis de politiques IAM attribuées aux utilisateurs/groupes
	  - Contrôle précis et facile des droits des utilisateurs dans les comptes AWS

- **Audit et sécurité renforcée**
	  - Logs centralisés pour chaque connexion et action effectuée via CloudTrail
	  - Meilleure traçabilité et facilité d’audit (qui, quand, quelle ressource)

- **Cas d’usage** :
	  - Simplifier l’accès dans les organisations ayant plusieurs comptes AWS
	  - Centraliser et sécuriser l’authentification utilisateur (éviter la gestion d’utilisateurs IAM individuels dans chaque compte)
	  - Réduire les risques d’erreur humaine et améliorer la conformité (audit facilité)

- **Pour l’examen AWS SAA** :
	  - Peu fréquent, mais important de comprendre que c’est la solution recommandée pour le SSO et la gestion centralisée d’identités en environnement multi-comptes
	  - À ne pas confondre avec IAM classique, qui est spécifique à chaque compte individuel

## AWS Directory Service
AWS Directory Service est un ensemble de solutions managées permettant de configurer et d'utiliser des services d’annuaire (type Active Directory) dans AWS. Cela facilite l'intégration avec les applications existantes, la gestion centralisée des utilisateurs, et simplifie l'authentification unique (**SSO**) pour les applications et services AWS.

- **Services proposés (types d'annuaires) :**
  - **AWS Managed Microsoft AD**
    - Active Directory entièrement managé par AWS (compatible AD classique)
    - Supporte Group Policy Objects (GPO), authentification Windows intégrée, etc.
    - Idéal pour les entreprises souhaitant migrer ou étendre leur Active Directory vers AWS

  - **AD Connector**
    - Connecte un Active Directory existant (on-premise) à AWS, sans le déplacer
    - Authentification dans AWS à partir de votre AD existant, via un VPN ou Direct Connect

  - **Simple AD**
    - Service compatible AD simplifié basé sur Samba
    - Moins riche en fonctionnalités que Microsoft AD, mais moins cher et adapté à des besoins basiques (gestion utilisateurs, DNS interne, authentification)

  - **Cloud Directory**
    - Annuaire évolutif conçu pour applications cloud natives (idéal pour structures de données hiérarchiques complexes)
    - Ex : gestion de hiérarchies organisationnelles, appareils IoT, autorisations granulaires dans des applications SaaS

  - **Cognito User Pools**
    - Pour applications mobiles/web, gère des utilisateurs sans nécessité d'un annuaire d'entreprise complet
    - Fournit authentification, autorisation, MFA et intégration avec services sociaux (Google, Facebook...)

- **Intégrations fréquentes avec AWS :**
	  - IAM Identity Center (SSO)
	  - WorkSpaces, WorkDocs, AppStream 2.0
	  - EC2 (accès Windows via domaine AD)
	  - Amazon RDS, FSx for Windows File Server (intégration directe à AD)

- **Cas d’usage :**
	  - Centralisation de l'authentification et gestion des identités pour applications et ressources AWS
	  - Migration ou extension d’un Active Directory existant vers le cloud
	  - Mise en place rapide d’un annuaire simplifié pour PME ou environnements de test
	  - Authentification unique pour applications cloud via intégration avec Identity Center

- **Pour l’examen AWS SAA :**
	  - Identifier AWS Directory Service comme la solution managée pour Active Directory (et équivalents) dans AWS
	  - Important de distinguer clairement les cas d'usage entre Managed Microsoft AD (full AD), Simple AD (basique) et AD Connector (connectivité avec on-premise)
	  - Peut apparaître en lien avec la sécurité, la migration cloud, ou l’intégration d’identités on-premise/cloud

## AWS Control Tower
AWS Control Tower est un service AWS qui simplifie considérablement la mise en place, la gestion, et la gouvernance d'un environnement AWS multi-comptes sécurisé. Il permet notamment de créer rapidement une **landing zone** (zone d’atterrissage) selon les bonnes pratiques AWS, pour déployer, contrôler et auditer plusieurs comptes au sein d’une organisation.

- **Déploiement automatisé d’une Landing Zone**
	  - Met en place rapidement un environnement multi-comptes structuré et sécurisé
	  - Utilise les bonnes pratiques AWS dès la création (réseaux, IAM, logging, etc.)

- **Gestion centralisée des comptes AWS**
	  - Création rapide de nouveaux comptes AWS à partir d'un compte « master »
	  - Structure prédéfinie avec des Unités Organisationnelles (OUs) pour isoler logiquement les comptes (Production, Développement, Audit, etc.)

- **Guardrails (garde-fous)**
	  - Ensemble de règles préconfigurées et personnalisables pour assurer la conformité des comptes
	  - Basé notamment sur les SCP (Service Control Policies) d’AWS Organizations et AWS Config Rules
	  - Exemples de Guardrails : empêcher la désactivation de CloudTrail, restreindre l’ouverture de ports publics, forcer le chiffrement des données

- **Tableau de bord centralisé**
	  - Vue unifiée de la conformité de tous les comptes
	  - Identifie rapidement les comptes non conformes et leurs raisons

- **Intégrations**
	  - AWS Organizations (gestion multi-comptes)
	  - AWS Config (surveillance continue)
	  - CloudTrail (audits des appels d’API centralisés)
	  - IAM Identity Center (SSO pour les utilisateurs)

- **Cas d’usage**
	  - Environnements d’entreprise avec plusieurs équipes ou divisions nécessitant des comptes AWS séparés et standardisés
	  - Mise en conformité rapide d'une organisation existante ou création simplifiée de nouveaux comptes
	  - Gouvernance efficace des environnements cloud, notamment en contexte réglementaire strict (PCI-DSS, ISO, HIPAA)

- **Pour l’examen AWS SAA**
	  - Savoir identifier Control Tower comme solution clé pour gérer et gouverner un environnement multi-comptes AWS
	  - Différencier clairement Control Tower d'AWS Organizations seul (Control Tower offre plus d'automatisation et une intégration poussée avec d'autres services AWS)
	  - Comprendre le concept de Landing Zone et Guardrails
