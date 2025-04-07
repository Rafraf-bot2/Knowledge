# Networking
## Résumé
### 🧠 **Fondamentaux réseau**
- **CIDR** : plage d’adresses IP (IPv4 ou IPv6) utilisée pour définir la taille du réseau
- **VPC (Virtual Private Cloud)** : ton réseau privé dans AWS, où tu définis des CIDR IPv4/IPv6
- **Subnets** : segments de réseau dans une AZ, avec leur propre CIDR

### 🌐 **Accès Internet et Routage**
- **Internet Gateway (IGW)** : fournit l’accès Internet (IPv4/IPv6) à un VPC (via subnets publics)
- **Route Tables** : définissent les chemins du trafic réseau (vers IGW, NATGW, VPC Peering, etc.)
- **Egress-Only Internet Gateway** : comme un NAT Gateway mais pour **IPv6 uniquement** (sortant seulement)

### 🔐 **Sécurité**
- **Security Groups** : *stateful*, appliqués au niveau des **instances EC2**
- **NACLs (Network ACLs)** : *stateless*, appliqués au niveau **des subnets** (gèrent Inbound/Outbound)
- ⚠️ **Ephemeral Ports** : penser à les ouvrir dans les NACLs pour les retours de connexion

### 🧳 **Accès aux instances privées**
- **Bastion Host** : EC2 dans un subnet public utilisé pour SSH vers des instances privées
- **NAT Instance** : ancienne méthode (non managée) pour permettre l’accès Internet à des instances privées (nécessite désactivation du *Source/Dest Check*)
- **NAT Gateway** : version managée et scalable du NAT, pour **IPv4 uniquement**

### 🔗 **Connexion entre réseaux**
- **VPC Peering** : connexion directe entre deux VPC sans chevauchement de CIDR (non transitive)
- **Transit Gateway** : routeur centralisé pour connecter plusieurs VPC, VPN, Direct Connect (**transitif**)
- **VPC Endpoints** : accès privé aux services AWS (S3, DynamoDB, SSM…) **sans IGW ni NAT**
	  - **Gateway Endpoint** : S3 et DynamoDB uniquement
	  - **Interface Endpoint** : tous les autres services (via PrivateLink)

### 🔍 **Observation et analyse du trafic**
- **VPC Flow Logs** : capture les métadonnées du trafic réseau (ACCEPT / REJECT) pour analyse (CloudWatch Logs, Athena)
- **Traffic Mirroring** : copie des paquets réseau réels (L3/L4) pour analyse approfondie (ex : IDS/IPS)

### 🌐 **Connexion avec l’extérieur**
- **Site-to-Site VPN** : tunnel IPsec chiffré entre ton datacenter (Customer Gateway) et un VPC (Virtual Private Gateway ou Transit Gateway)
- **VPN CloudHub** : modèle hub-and-spoke VPN pour connecter plusieurs sites via VGW
- **Direct Connect (DX)** : connexion réseau privée, stable, rapide (non chiffrée nativement)
	  - **Direct Connect Gateway** : permet de partager une connexion DX avec **plusieurs VPC dans plusieurs régions**

### 📡 **PrivateLink / Endpoint Services**
- **AWS PrivateLink** : permet à un client d'accéder **privément à ton service** via un **Interface Endpoint**
	  - Ne nécessite **ni peering**, ni **IGW**, ni **route table modifiée**
	  - Requiert un **Network Load Balancer** et une **ENI**

### 🧓 **Legacy (ancien)**
- **ClassicLink** : permet de connecter des **EC2-Classic** à un VPC (service désormais obsolète)

---

## VPC Overview
- **VPC = réseau privé virtuel dans le cloud AWS**  
	  - C’est un espace réseau **isolé logiquement** où tu déploies tes ressources AWS (EC2, RDS, etc.)  
	  - Tu contrôles **l’adressage IP, le routage, la sécurité réseau** (firewalls, ACLs, etc.)

- **Caractéristiques principales**  
	  - Tu peux avoir **plusieurs VPCs par région** (max 5 par défaut, extensible via quota)  
	  - Chaque VPC a un **CIDR (plage d'adresses IP)** :  
		    - Tu peux associer **jusqu’à 5 CIDR blocks** par VPC  
		    - **Taille minimale : /28** (16 adresses)  
		    - **Taille maximale : /16** (65 536 adresses)  
		    - **Important** : ton CIDR **ne doit pas chevaucher** d'autres réseaux (ex : ton réseau entreprise)

- **Plages IP autorisées (IPv4 privées uniquement)**  
	  - 10.0.0.0/8 → très courant pour les grands réseaux  
	  - 172.16.0.0/12  
	  - 192.168.0.0/16  
	  - Ce sont les **plages IP privées** définies par la RFC 1918 (utilisables en interne uniquement)

- **Pourquoi c’est important ?**  
	  - Le VPC te donne un **contrôle total sur ton réseau AWS**  
	  - Tu peux le **connecter à ton datacenter** (VPN ou Direct Connect)  
	  - Tu définis **les sous-réseaux (publics ou privés)**, les **routages**, et les **groupes de sécurité**

- **Pour l’examen AWS SAA**  
	  - Très **fréquent**, car **presque toutes les architectures AWS passent par un VPC**  
	  - Tu dois bien connaître les **plages IP privées**, les tailles de CIDR, et le fait qu’un VPC est **isolé** sauf si tu l’ouvres (VPN, Peering, etc.)

## VPC Subnets
- **Un subnet est une sous-réseau défini dans un VPC**  
	  - Il permet de **segmenter l’espace IP du VPC** en blocs plus petits  
	  - Chaque subnet est **associé à une zone de disponibilité (AZ)**  
	  - Tu peux créer des **subnets publics** (accès internet via IGW) ou **privés** (accès uniquement interne)

- **AWS réserve 5 adresses IP dans chaque subnet**  
	  - Ces adresses **ne sont pas disponibles pour les ressources** (EC2, etc.)  
	  - Exemple avec un subnet `10.0.0.0/24` (256 adresses) :  
		    - `10.0.0.0` – **adresse réseau**  
		    - `10.0.0.1` – **routeur VPC**  
		    - `10.0.0.2` – **DNS interne AWS**  
		    - `10.0.0.3` – **réservée pour usage futur**  
		    - `10.0.0.255` – **adresse de broadcast** (même si le broadcast n’est pas utilisé dans AWS, cette IP est quand même réservée)

- **Impact sur la capacité IP réelle**  
	  - Si tu crées un subnet `/24`, tu as **256 – 5 = 251 IP utilisables**  
	  - Si tu crées un subnet `/27`, tu as **32 – 5 = 27 IP utilisables**  
	  - Donc, **si tu veux héberger 29 EC2 instances**, un `/27 ne suffit pas**  
		    - Tu dois utiliser au moins un **/26 (64 adresses – 5 = 59 utilisables)**

- Cas d’usage  
	  - Créer un **subnet public** dans une AZ pour des ressources accessibles depuis Internet  
	  - Créer plusieurs **subnets privés** dans différentes AZ pour la haute disponibilité  
	  - Réserver de **petits subnets** pour des services internes, ou de **grands subnets** pour des clusters auto-scaling

- **Pour l’examen AWS SAA**  
	  - Question **très fréquente**, notamment sur le calcul des IP utilisables dans un subnet  
	  - Bien retenir que **5 IP sont toujours réservées** et **ne peuvent pas être utilisées**  
	  - Savoir faire un calcul rapide pour déterminer **la bonne taille de subnet** selon le besoin (ex. 29 IP requises ⇒ /26)

## Bastion Hosts
- **Un Bastion Host permet d’accéder en SSH aux instances privées dans un VPC**  
	  - C’est une **instance EC2 placée dans un subnet public**  
	  - Elle sert de **point d’entrée sécurisé** vers les instances **dans des subnets privés**, qui ne sont **pas directement accessibles depuis Internet**

- **Fonctionnement**  
	  - Tu te connectes en **SSH (port 22)** à l’instance bastion depuis l’extérieur (ton réseau d’entreprise, par exemple)  
	  - Depuis le bastion, tu te connectes ensuite en SSH aux instances privées (dans les autres subnets du VPC)  
	  - Le bastion agit comme **passerelle** sans exposer directement tes EC2 sensibles à Internet

- **Configuration réseau et sécurité**  
	  - Le **bastion host est placé dans un subnet public** avec une **Elastic IP** et une **route vers l’Internet Gateway**  
	  - Son **Security Group doit autoriser l’accès en SSH (port 22) depuis un CIDR restreint** (ex : l’IP publique de ton entreprise)  
	  - Le **Security Group des instances privées** doit autoriser :  
		    - soit **le SG du bastion** (`source: sg-xxxxxxxx`)  
		    - soit l’**adresse IP privée** du bastion (plus rigide, moins recommandé)

- Cas d’usage  
	  - Accéder à une base de données EC2 dans un subnet privé sans exposer le port DB publiquement  
	  - Administrer des serveurs d’applications ou d’analyse dans un environnement **entièrement privé**  
	  - Réduire **la surface d’exposition réseau** : une seule IP publique, tous les accès centralisés


- **Pour l’examen AWS SAA**  
	  - Question **fréquente** dans les scénarios d’architecture sécurisée  
	  - Bien comprendre que **seul le bastion est exposé**, le reste du réseau est **isolé dans des subnets privés**  
	  - Savoir configurer correctement les **Security Groups entre bastion et instances privées**

## NAT Gateway
- **NAT Gateway permet aux instances dans des subnets privés d’accéder à Internet sans être exposées**  
	  - Idéal pour que des EC2 **dans un subnet privé** puissent faire des mises à jour, appels API, ou télécharger des paquets  
	  - Le trafic sortant passe par la NAT Gateway, mais **aucun trafic entrant depuis Internet n’est autorisé**

- **Fonctionnement et architecture**  
	  - **AWS gère entièrement la NAT Gateway** (scalabilité, maintenance, HA dans une AZ)  
	  - Elle est **déployée dans un subnet public** avec une **Elastic IP**  
	  - Le subnet privé doit avoir une **route vers la NAT Gateway**, qui elle-même **accède à Internet via l’IGW**  
		    - Flux : `Instance privée → NATGW (subnet public) → IGW → Internet`

- **Spécificités techniques**  
	  - **5 Gbps de bande passante par défaut**, **scalabilité automatique jusqu’à 100 Gbps**  
	  - **Aucun Security Group requis** (contrairement aux EC2 ou Bastion Host)  
	  - **Non utilisable par les instances dans le même subnet** que la NATGW  
	  - **Facturation à l’heure + par Go transféré** (peut coûter cher à long terme si mal dimensionné)

- **NAT Gateway est hautement disponible… mais uniquement dans une seule AZ**  
	  - **Gérée par AWS**, elle est **résiliente au sein de son AZ**  
	  - Si tu la déploies dans `us-east-1a`, elle est capable de gérer le trafic pour tous les subnets de cette zone

- **Pas de failover inter-AZ automatique**  
	  - **Chaque NAT Gateway est locale à son AZ**  
	  - Il n’y a **aucune bascule automatique entre zones** en cas de panne  
	  - Donc : **si une AZ tombe**, les instances dans cette AZ **n’auront plus besoin d'accéder à Internet** (car elles sont inaccessibles de toute façon)

- **Bonne pratique : déployer une NAT Gateway par AZ utilisée**  
	  - Pour garantir la **continuité du trafic Internet sortant** dans chaque AZ active  
	  - Chaque **subnet privé dans une AZ** doit avoir une **route vers la NATGW de la même AZ**  
	  - Évite que toutes les instances dépendent d’une seule NAT Gateway dans une autre zone

- Cas d’usage  
	  - Permettre à un **serveur d’application** ou à un **batch script** d’accéder à Internet depuis un subnet privé  
	  - Sécuriser une architecture en empêchant tout accès entrant vers les EC2  
	  - Télécharger des mises à jour de paquets, envoyer des logs, accéder à des APIs publiques sans exposer l’IP publique des instances

- **Pour l’examen AWS SAA**  
	  - **Très courant** dans les questions d’architecture réseau  
	  - Bien retenir que :  
		    - NATGW = pour **trafic sortant depuis subnet privé**  
		    - **Nécessite un IGW** (Internet Gateway)  
		    - **Doit être dans un subnet public**  
		    - **Ne peut pas être utilisée par des EC2 dans le même subnet**
  - Important de comprendre que la **NAT Gateway est zonale**, donc **il faut en créer une par AZ** pour la haute dispo  
  - Savoir qu’**il n’y a pas de failover inter-AZ**, car en cas de panne d'une AZ, ses instances sont inaccessibles

## NACLs

- **Les NACLs sont comme des firewalls stateless, appliqués au niveau des subnets**  
	  - Elles **contrôlent le trafic entrant et sortant** des subnets (contrairement aux Security Groups qui s’appliquent aux instances)  
	  - Chaque **subnet ne peut être associé qu’à une seule NACL**  
	  - Les **nouveaux subnets** utilisent par défaut la **NACL par défaut** (qui accepte tout)

- **Fonctionnement des règles NACL**  
	  - Tu définis des règles **numérotées de 1 à 32766**  
		    - **Les numéros les plus bas ont la priorité**  
		    - La première règle qui correspond est **appliquée** (pas de vérification des suivantes)  
	  - Exemple :  
		    - Règle #100 : ALLOW `10.0.0.10/32`  
		    - Règle #200 : DENY `10.0.0.10/32`  
		    - **Résultat : autorisé**, car la règle 100 est prioritaire  
	  - La **dernière règle implicite** est un `*` → **DENY** si aucune autre règle ne correspond  
	  - AWS recommande d’**incrémenter les règles de 100** (pour laisser de la place à d'autres)

- **NACL par défaut vs personnalisée**  
	  - La **NACL par défaut accepte tout le trafic** entrant et sortant  
	  - **Ne pas la modifier directement** : crée plutôt une **NACL personnalisée** adaptée à ton besoin  
	  - Une **nouvelle NACL personnalisée bloque tout par défaut**, jusqu’à ce que tu ajoutes tes propres règles

- **Caractéristiques clés**  
	  - **Stateless** : les règles de sortie doivent être définies **indépendamment** des règles d’entrée  
	  - Pratique pour **bloquer une IP spécifique au niveau du subnet**  
	  - Utilisable pour des politiques de sécurité larges, par exemple **bloquer des régions géographiques ou des ranges IP**

- **Pour l’examen AWS SAA**  
	  - Savoir que **NACL ≠ Security Groups** :  
		    - **NACLs = stateless, au niveau subnet**  
		    - **SGs = stateful, au niveau instance**  
	  - Bien retenir que **la priorité est définie par le numéro de la règle**, et que **la première correspondance gagne**  
	  - Connaître la **différence entre NACL par défaut (ALLOW ALL)** et les nouvelles NACL (DENY ALL jusqu’à config)

### NACLs VS Security Groups

| **Critère**                         | **Security Groups**                                            | **NACLs (Network ACLs)**                                       |
|------------------------------------|----------------------------------------------------------------|----------------------------------------------------------------|
| **Niveau d’application**           | Au **niveau des instances** (EC2)                              | Au **niveau des subnets**                                      |
| **Type de règles**                 | **Uniquement des règles ALLOW**                               | **Règles ALLOW et DENY**                                       |
| **Comportement**                   | **Stateful** : le trafic de retour est automatiquement autorisé | **Stateless** : il faut **autoriser explicitement** le trafic retour |
| **Évaluation des règles**          | **Toutes les règles sont évaluées** avant de décider           | Les règles sont évaluées **dans l’ordre (du plus petit au plus grand numéro)**, le **premier match l’emporte** |
| **Application aux ressources**     | **S’applique à une instance EC2** lorsqu’on lui assigne le SG  | **S’applique automatiquement à toutes les instances du subnet associé** |

- **Pour l’examen AWS SAA**  
	  - Question **très fréquente**  
	  - Savoir que **Security Groups = pare-feu stateful**, et **NACLs = pare-feu stateless**  
	  - SG : bon pour le contrôle **fin au niveau des instances**  
	  - NACL : bon pour **bloquer des IPs** ou **contrôler le trafic global à un subnet**

## VPC Peering
- **VPC Peering permet de connecter deux VPC de manière privée via le réseau AWS**  
	  - Les deux VPC peuvent alors **échanger du trafic comme s’ils étaient sur le même réseau interne**  
	  - Utilisé pour **faire communiquer des instances EC2 entre VPC**, sans passer par Internet ou VPN

- **Caractéristiques importantes**  
	  - **Pas de CIDR qui se chevauche** : les plages d’IP des deux VPC doivent être distinctes  
		  - La connexion **n’est pas transitive** :  
			    - Si VPC-A est peeré avec VPC-B, et VPC-B avec VPC-C → **A ne peut pas communiquer avec C**  
			    - Il faut établir un **VPC Peering explicite entre chaque paire de VPC**

	- **Configuration à faire après création**  
		  - **Mise à jour des route tables** dans les deux VPC pour acheminer le trafic via la connexion de peering  
		  - Tu peux aussi **référencer un Security Group d’un VPC peeré**, **dans la même région** et même **entre comptes différents**

	- **Types de peering possibles**  
		  - **Intra-région** : entre deux VPC dans la même région AWS  
		  - **Inter-région** : entre deux VPC situés dans des **régions différentes**  
		  - **Inter-comptes** : tu peux peerer des VPC appartenant à **des comptes AWS distincts**

- Cas d’usage  
	  - Connecter un **VPC applicatif** avec un **VPC contenant une base de données**  
	  - Communication entre environnements **de développement/test et production**  
	  - Partager un **service centralisé** (ex : bastion, proxy, outil de monitoring) entre plusieurs VPC  

- **Pour l’examen AWS SAA**  
	  - Question **fréquente**, notamment en architecture multi-VPC ou multi-comptes  
	  - Bien retenir :  
		    - Pas de **chevauchement de CIDR**  
		    - **Connexion non transitive**  
		    - Nécessité de **mettre à jour les route tables**  
		    - Possibilité de faire du peering **inter-région** et **inter-comptes**

## VPC Endpoints
- **VPC Endpoints permettent d’accéder aux services AWS sans passer par Internet**  
	  - Le trafic reste **entièrement dans le réseau privé AWS**, **sans passer par une IGW**, NAT Gateway, ni par une IP publique  
	  - Idéal pour sécuriser les accès aux services AWS depuis des **EC2 dans des subnets privés**

- **Deux types de VPC Endpoints**  
	  - **Interface Endpoint (powered by PrivateLink)**  
		    - Crée une **ENI (Elastic Network Interface)** dans ton subnet  
		    - Utilisé pour **accéder à des services AWS (comme SSM, KMS, Secrets Manager)** ou à des services **privés (PrivateLink)**  
		    - Chaque interface a une **adresse IP privée** dans le VPC  
		    - Supporte **les security groups**  
		    - Peut être utilisé pour **accéder à un service AWS dans une autre VPC (PrivateLink)**

	  - **Gateway Endpoint**  
		    - Utilisé uniquement pour **S3 et DynamoDB**  
		    - Ajoute une entrée dans la **route table** du VPC  
		    - **Pas d’ENI ni de security group**  
		    - Plus simple et **sans frais supplémentaires** (contrairement aux Interface Endpoints)

- **Pourquoi utiliser un VPC Endpoint ?**  
	  - **Sécurité accrue** : pas d’exposition à Internet, pas de trafic sortant  
	  - **Économie** : évite de payer une NAT Gateway pour des appels internes à AWS  
	  - **Performance** : latence plus faible car le trafic reste sur le réseau interne AWS  
	  - **Contrôle fin** : tu peux restreindre l’accès avec des **politiques d’Endpoint** ou des **IAM conditions**

- Cas d’usage  
	  - Une **EC2 dans un subnet privé** qui accède à **S3** ou **Secrets Manager**  
	  - Un **container ECS** qui télécharge des objets S3 sans passer par une NAT Gateway  
	  - Intégrer un **service tiers exposé via PrivateLink** dans ton VPC

- **Pour l’examen AWS SAA**  
	  - Notion **très fréquente** dans les scénarios **de sécurité réseau, VPC, et économies de coûts**  
	  - Bien connaître la **différence entre Interface Endpoint (privé + ENI + services divers)** et **Gateway Endpoint (S3/DynamoDB)**  
	  - Retenir que **les Interface Endpoints utilisent PrivateLink** et que tu peux les **protéger avec des Security Groups**

## VPC Flow Logs
- **VPC Flow Logs permettent de capturer les métadonnées du trafic réseau dans un VPC**  
	  - Ils enregistrent les **flux IP** (source, destination, ports, protocole, action, etc.)  
	  - Ne capturent **pas le contenu des paquets**, uniquement les **informations de session réseau** (niveau L3/L4)

- **Ce que tu peux surveiller avec VPC Flow Logs**  
	  - **Trafic entrant et sortant** sur :  
		    - Une **interface réseau (ENI)**  
		    - Un **subnet**  
		    - Un **VPC entier**  
	  - Tu vois :  
		    - **Adresse source et destination**  
		    - **Port et protocole**  
		    - **Action (ACCEPT / REJECT)**  
		    - **Nombre d’octets et de paquets**, etc.

- **Destination des logs**  
	  - Tu peux exporter les logs vers :  
		    - **Amazon CloudWatch Logs** (analyse en temps réel)  
		    - **Amazon S3** (archivage, traitement batch)  
		    - **Kinesis Data Firehose** (streaming vers SIEM, etc.)

- **Cas d’usage**  
	  - **Déboguer des problèmes de connectivité** (ex : pourquoi une EC2 ne peut pas contacter un service)  
	  - **Auditer le trafic réseau** pour la conformité ou la sécurité  
	  - **Détecter des comportements anormaux** (ex : scans de ports, exfiltration de données)  
	  - **Analyser les flux rejetés** par des NACLs ou Security Groups

- **Bonnes pratiques**  
	  - Activer les logs **au bon niveau de granularité** (VPC = global, ENI = précis)  
	  - **Filtrer** ce que tu captures (via les nouveaux Flow Logs avec filtre avancé) pour éviter des coûts excessifs  
	  - Les logs peuvent **consommer beaucoup d’espace**, attention aux coûts si export vers S3 ou CloudWatch  

- **Pour l’examen AWS SAA**  
	  - Savoir que **VPC Flow Logs = outil d’analyse de trafic réseau au niveau du VPC / subnet / ENI**  
	  - Ne contient **pas le contenu réseau**, seulement les **métadonnées du trafic**  
	  - Peut être utilisé pour **auditer ou diagnostiquer un problème de connectivité ou sécurité**  
	  - **Pas en temps réel**, mais souvent utilisé avec **CloudWatch Logs Insights** pour interrogation

## AWS Site-to-Site VPN
- **AWS Site-to-Site VPN permet de connecter ton réseau on-premise à un VPC AWS via un tunnel VPN sécurisé**  
	  - Connexion réseau **chiffrée en IPsec**  
	  - Utilisée pour **étendre ton infrastructure locale vers le cloud AWS**

- **Fonctionnement**  
	  - Création d’un **Virtual Private Gateway (VGW)** ou **Transit Gateway (TGW)** côté AWS  
	  - Configuration d’un **Customer Gateway (CGW)** représentant ton routeur physique ou logiciel sur site  
	  - AWS établit **deux tunnels VPN redondants** pour la **haute disponibilité**  
	  - Le trafic entre ton datacenter et AWS est **chiffré via IPsec** (niveau réseau)

- **Caractéristiques techniques**  
	  - **Jusqu’à 1,25 Gbps** de bande passante par tunnel (variable selon ton équipement côté client)  
	  - **Chiffrement IPsec**, compatible avec la plupart des routeurs/firewalls  
	  - **Failover automatique** entre les deux tunnels si l’un tombe  
	  - Possibilité de **monitorer les tunnels** via **CloudWatch Metrics**

- **Options de connexion**  
	  - **Via Virtual Private Gateway (VGW)** : attaché à un seul VPC  
	  - **Via Transit Gateway (TGW)** : pour partager la connexion VPN entre **plusieurs VPC** (plus flexible)  
	  - Peut être combiné avec **Direct Connect** pour une **connexion hybride haut débit + secours VPN**

- Cas d’usage  
	  - **Connexion sécurisée entre ton datacenter et AWS**  
	  - Étendre une application locale vers le cloud (cloud bursting)  
	  - Mise en place d’un environnement hybride temporaire (migration)  
	  - **Backup** d’une connexion Direct Connect principale

- **Pour l’examen AWS SAA**  
	  - Savoir que **Site-to-Site VPN = tunnel IPsec sécurisé entre un réseau on-premise et AWS**  
	  - Comprendre la **différence entre VGW et TGW** pour le rattachement  
	  - Important de savoir que **la connexion est redondante (2 tunnels)**, mais **dépend de la qualité du lien Internet** côté on-premise  
	  - **Direct Connect = connexion privée haut débit**, **VPN = rapide à mettre en place mais dépend d'Internet**

## Direct Connect (DX)
- **AWS Direct Connect est une connexion réseau privée dédiée entre ton datacenter (ou bureau) et AWS**  
	  - Elle **ne passe pas par Internet**, donc **plus fiable, plus rapide et plus sécurisée** qu’un VPN  
	  - Fournit une **bande passante stable** et une **latence faible**, idéale pour les charges critiques

- **Fonctionnement**  
	  - Tu établis une liaison physique (fibre optique) entre ton infrastructure locale et un **AWS Direct Connect Location**  
	  - Cette liaison est **terminée côté AWS** sur un **Virtual Interface (VIF)** qui te donne accès à :  
		    - **Un VPC** via un **Virtual Private Gateway**  
		    - Ou à **plusieurs VPC** via un **Transit Gateway**  
	  - Direct Connect ne chiffre pas nativement les données → **on peut le combiner avec un VPN IPsec pour plus de sécurité**

- **Avantages par rapport à Site-to-Site VPN**  
	  - **Fiabilité réseau supérieure**, pas dépendant d’Internet  
	  - **Débit garanti** (de 50 Mbps à 100 Gbps selon le port choisi)  
	  - **Coûts réduits** sur le transfert de données sortantes (moins cher qu’Internet)

- **Deux types de virtual interfaces (VIFs)**  
	  - **Private VIF** : pour accéder à un VPC  
	  - **Public VIF** : pour accéder aux services publics AWS (ex : S3, DynamoDB) avec une IP publique AWS, sans passer par Internet  
	  - Tu peux avoir **plusieurs VIFs sur la même connexion physique**

- Cas d’usage  
	  - Connexion fiable et rapide à AWS pour des **bases de données critiques ou systèmes ERP**  
	  - **Synchronisation massive de données** ou réplication entre systèmes  
	  - Accès aux **services AWS publics** avec performance garantie (ex : traitement big data depuis un datacenter)

- **Pour l’examen AWS SAA**  
  - Bien connaître la différence entre **Site-to-Site VPN (rapide à mettre en place, via Internet)** et **Direct Connect (lien dédié)**  
  - Savoir que Direct Connect **n’est pas chiffré par défaut** → souvent **combiné avec un VPN pour la sécurité**  
  - Comprendre que **Direct Connect peut se connecter à un VPC directement (via VGW)** ou **via Transit Gateway pour plusieurs VPC**

## Transit Gateway
- **Transit Gateway est un routeur cloud centralisé pour connecter plusieurs VPC et réseaux on-premise**  
	  - C’est un **hub réseau** qui simplifie les connexions : **un-to-many** au lieu de **many-to-many**  
	  - Permet d’éviter la complexité des **peering en étoile** (full mesh entre VPC)

- **Fonctionnement**  
	  - Tu connectes des **VPC**, **VPNs Site-to-Site**, **AWS Direct Connect** ou même **Transit Gateways inter-régions**  
	  - Tous ces réseaux communiquent **via un point central unique**  
	  - Il est **routable et scalable automatiquement**, et gère **jusqu'à des milliers de connexions**

- **Comportement par défaut**  
	  - **Pas de routage automatique entre VPC connectés** :  
		    - Tu dois configurer des **Route Tables spécifiques par attachement**  
		    - Très utile pour **contrôler les flux réseau (isolation, segmentation)**

- **Fonctionnalités avancées**  
	  - **Propagation de routes** automatique (optionnelle)  
	  - **Isolation du trafic** via plusieurs **tables de routage**  
	  - **Partage possible entre comptes AWS via AWS RAM**  
	  - **Peering entre Transit Gateways de différentes régions** (communication inter-région haut débit)


- **Cas d’usage**  
	  - Remplacer des dizaines de **VPC Peering complexes** par une architecture centralisée  
	  - **Connecter plusieurs VPC d’un même ou de plusieurs comptes** à une connexion VPN ou Direct Connect unique  
	  - Créer une architecture **hub-and-spoke** pour séparer les environnements (Prod / Dev / Audit)  
	  - Mise en place d’un **réseau hybride global multi-régions** avec routage simplifié

- **Pour l’examen AWS SAA**  
	  - Très **souvent testé** dans les scénarios **multi-VPC, intercomptes, ou hybrides**  
	  - Savoir que **Transit Gateway ≠ VPC Peering** :  
		    - **Transit Gateway est scalable, centralisé, et non transitive "by default"**  
		    - **VPC Peering est point à point, non transitive**  
	  - Comprendre que Transit Gateway **peut se connecter à Direct Connect et VPN**, et gérer **plusieurs tables de routage**

## VPC Traffic Mirroring
- **VPC Traffic Mirroring permet de capturer le trafic réseau réel (paquets) d’interfaces réseau dans ton VPC**  
	  - C’est une fonctionnalité de **monitoring avancé**, différente des VPC Flow Logs (qui ne capturent que les métadonnées)  
	  - Tu peux **répliquer le trafic entrant et sortant** d’une **ENI (Elastic Network Interface)** vers une **instance de monitoring ou une appliance** dans AWS

- **Fonctionnement**  
	  - Tu définis une **source** : l’ENI à surveiller (souvent une instance EC2 critique)  
	  - Tu définis une **destination** : l’ENI d’une instance (par exemple avec Wireshark, Zeek, Suricata…)  
	  - Tu peux filtrer le trafic (par port, protocole, CIDR) via des **Traffic Mirror Filters**  
	  - Nécessite que les **instances source et destination soient dans le même VPC ou VPC peeré**

- **Caractéristiques clés**  
	  - Capture **le trafic brut (niveau paquet)**, utile pour :  
		    - **Déboguer des applications réseau**  
		    - **Faire de l’analyse de sécurité poussée**  
		    - **Détecter des attaques ou comportements malveillants**  
	  - **Consomme de la bande passante** → attention au coût et à l’impact sur les performances  
	  - Ne supporte **que les instances EC2 Nitro (récentes)** comme sources (pas toutes les familles EC2)

- **Différences avec VPC Flow Logs**  

  | **Fonction**              | **VPC Flow Logs**                          | **Traffic Mirroring**                          |
  |---------------------------|---------------------------------------------|------------------------------------------------|
  | Type de données           | Métadonnées réseau                         | Paquets réseau complets                        |
  | Niveau                    | VPC, Subnet ou ENI                         | ENI uniquement                                 |
  | Utilisation               | Audit, conformité, connectivité            | Détection d’anomalies, inspection approfondie  |
  | Volume généré             | Léger                                      | Potentiellement très important                 |

- **Cas d’usage**  
	  - **Détection d'intrusion (IDS/IPS)** avec une appliance de sécurité  
	  - **Analyse de performance réseau** ou problèmes d’application  
	  - **Forensic** après incident de sécurité  
	  - Environnements très sécurisés (finance, santé…)

- **Pour l’examen AWS SAA**  
	  - Moins fréquent, mais **utile à connaître pour les cas de sécurité avancée**  
	  - Bien différencier **Flow Logs (métadonnées)** de **Traffic Mirroring (trafic réel)**  
	  - Comprendre que ça **s’applique à des ENIs**, et que **la cible est une instance avec un outil d’analyse**

## Egress Only Internet Gateway
- **Un Egress-Only Internet Gateway (EOIGW) permet aux instances IPv6 dans un VPC privé d’accéder à Internet en sortie uniquement**  
	  - Il est utilisé **exclusivement avec IPv6**  
	  - Il permet le **trafic sortant** vers Internet, mais **bloque tout le trafic entrant non sollicité**

- **Fonctionnement**  
	  - Similaire à une **NAT Gateway**, mais uniquement pour **IPv6**  
	  - Doit être associé à un **routeur (route table)** dans un **subnet privé avec des adresses IPv6**  
	  - Permet à ces instances de **naviguer sur Internet**, de faire des appels API ou des mises à jour, **sans être accessibles depuis l’extérieur**

- **Caractéristiques techniques**  
	  - **Pas d’adresse IP publique requise** sur l’instance  
	  - **Aucun coût direct pour l’Egress-Only IGW lui-même**, mais tu paies le **trafic sortant**  
	  - Ne gère que le **trafic IPv6**, pour l’IPv4 il faut une **NAT Gateway**

- **Cas d’usage**  
	  - Tu veux que des instances **EC2 dans un subnet privé en IPv6** accèdent à Internet (ex : mises à jour système)  
	  - Tu veux **bloquer complètement les connexions entrantes**, tout en autorisant les connexions sortantes  
	  - Tu veux économiser le coût d'une NAT Gateway dans une **architecture full IPv6**



- **Différences clés**  

  | **Fonction**                        | **Egress-Only IGW**     | **NAT Gateway**         |
  |-------------------------------------|--------------------------|--------------------------|
  | **Supporte IPv6 ?**                | ✅ Oui                   | ❌ Non                   |
  | **Supporte IPv4 ?**                | ❌ Non                   | ✅ Oui                   |
  | **Trafic entrant autorisé ?**      | ❌ Non                   | ❌ Non                   |
  | **Type d'accès**                   | Sortant uniquement       | Sortant uniquement       |
  | **Public IP nécessaire ?**         | ❌ Non                   | ✅ Oui (Elastic IP)      |


- **Pour l’examen AWS SAA**  
	  - À connaître pour les questions sur **les architectures IPv6 sécurisées**  
	  - Savoir que **Egress-Only IGW = NAT Gateway pour IPv6**, mais sans IP publique  
	  - Ne **pas confondre avec Internet Gateway classique**, qui autorise **trafic entrant et sortant**

## Networking Costs
- **Le trafic sortant (egress)** est **payant** : cela concerne tout ce qui sort d’AWS vers Internet, un réseau on-premise, ou une autre région  
- **Le trafic entrant (ingress)** est **gratuit** dans la majorité des cas : ex. données envoyées vers un bucket S3 depuis un client  

- **Utiliser des adresses IP privées** plutôt que publiques  
	  - Les **IP publiques** impliquent un **trafic via Internet Gateway ou NAT Gateway**, donc facturé  
	  - Le **trafic privé entre instances EC2 dans le même VPC est gratuit**

- **Placer les ressources dans la même zone de disponibilité (AZ)**  
	  - Le **trafic intra-AZ est gratuit**  
	  - Le **trafic inter-AZ (même région)** est **payant**, même à l’intérieur d’un même VPC  
	  - Utiliser la même AZ est **économique**, mais peut **nuire à la haute disponibilité**

- **Limiter le trafic vers Internet, privilégier les communications internes à AWS**  
	  - Préférer les services intégrés AWS (S3, DynamoDB, Lambda…) pour éviter les transferts vers l’extérieur  
	  - Utiliser des **VPC Endpoints ou AWS PrivateLink** pour accéder aux services AWS sans passer par Internet

- **Utiliser AWS Direct Connect dans la même région que ton VPC**  
	  - Réduit significativement les **coûts de transfert de données sortantes**  
	  - Offre une **connexion stable et rapide**, plus économique que le VPN ou les IP publiques

- **Pour l’examen AWS SAA**  
	  - Le **trafic intra-AZ avec IP privée est gratuit**  
	  - Le **trafic inter-AZ ou sortant est payant**  
	  - Les solutions comme **PrivateLink, VPC Endpoint, et Direct Connect** sont des **bonnes pratiques pour réduire les coûts réseau**

## AWS Network Firewall
- **Service managé de pare-feu réseau au niveau des VPC**  
	  - Fournit une **protection réseau avancée** avec inspection **stateful et stateless**  
	  - Permet de **filtrer, inspecter et bloquer** du trafic **entrant, sortant et inter-VPC**

- **Déployé dans un **subnet dédié appelé firewall subnet**  
	  - Nécessite un **subnet par AZ** où le trafic sera routé via le firewall  
	  - Tu **modifies la route table** des subnets pour envoyer le trafic vers le firewall  
	  - Compatible avec **Transit Gateway** pour filtrer le trafic entre VPCs et vers on-premise

- **Règles supportées**  
	  - **Stateless rules** : simples, s’appliquent immédiatement (ex. IP, port, protocole)  
	  - **Stateful rules** : suivent le contexte de connexion (ex. bloquer un retour de réponse HTTP anormal)  
	  - Supporte les formats **Suricata rules** (open source IDS/IPS)  
	  - Tu peux appliquer des règles sur le trafic **entrant, sortant, ou entre subnets**

- **Cas d’usage**  
	  - Appliquer une **sécurité centralisée et fine** sur un environnement multi-VPC  
	  - **Filtrer le trafic vers Internet** ou **vers un datacenter**  
	  - Prévenir les **exfiltrations de données**, les **communications non autorisées**, ou bloquer des **domaines/IP malveillants**  
	  - Complément de **Security Groups et NACLs**, mais avec **plus de puissance d’analyse et de filtrage**

- **Intégration et monitoring**  
	  - **CloudWatch Logs** pour analyser les journaux de flux et d’alertes  
	  - Peut être intégré à **Firewall Manager** pour appliquer des règles à **l’échelle de l’organisation AWS**  
	  - Gère la **haute disponibilité automatiquement** dans chaque AZ où il est déployé

- **Pour l’examen AWS SAA**  
	  - Moins fréquent que Security Groups ou NACLs, mais **important pour les architectures sécurisées à grande échelle**  
	  - Bien différencier :  
		    - **Security Groups** : instance-level, stateful  
		    - **NACLs** : subnet-level, stateless  
		    - **Network Firewall** : **VPC-level**, stateful/stateless, **règles avancées de sécurité réseau**  
	  - Savoir que **les firewalls sont dans des subnets dédiés**, et que **le routage vers eux est explicite**



