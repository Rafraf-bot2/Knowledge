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
