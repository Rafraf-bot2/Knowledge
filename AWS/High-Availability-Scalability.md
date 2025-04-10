# High Availability & Scalability

## Application Load Balancer (ALB) & Network Load Balancer

| **Critère**                        | **Application Load Balancer (ALB)**                                | **Network Load Balancer (NLB)**                                   |
|-----------------------------------|---------------------------------------------------------------------|-------------------------------------------------------------------|
| **Niveau OSI**                    | **Layer 7 (Application)**                                           | **Layer 4 (Transport)**                                           |
| **Protocoles supportés**         | HTTP, HTTPS, WebSocket                                              | TCP, TLS, UDP (et TCP_UDP)                                       |
| **Fonctionnalité principale**     | Routage basé sur **le contenu** (host, path, headers, etc.)        | Routage basé sur **l'adresse IP et le port**                     |
| **Performance**                   | Très bon pour **applications web**                                 | Conçu pour **haute performance / faible latence**                |
| **Cas d’usage typiques**          | Sites web, microservices, API REST                                 | Services temps réel, IoT, bases de données, applications à fort débit |
| **IP statique**                   | Non par défaut (nécessite Elastic IP via NLB devant)                | **Oui**, peut avoir une **Elastic IP fixe**                      |
| **TLS termination**               | Gérée nativement (HTTPS)                                           | Possible, mais **moins de flexibilité**                          |
| **Health checks**                | HTTP/HTTPS (niveau application)                                     | TCP (ou HTTP si cible ALB)                                       |
| **Support WebSocket / gRPC**      | **Oui**                                                             | Partiel / dépend du protocole                                    |
| **Cibles**                        | EC2, containers (ECS, EKS), IPs, Lambda                             | EC2, IPs, ECS, appliances réseau                                 |
| **Intégration Lambda**            | **Oui (directement)**                                               | Non                                                              |
| **Support multi-port**            | Non                                                                 | **Oui** (peut gérer plusieurs ports par cible)                   |
| **Coût**                          | Moins cher pour trafic HTTP simple                                 | Plus cher, mais meilleur pour trafic lourd ou bas niveau         |


- **ALB** : pour les **applications web**, le routage **intelligent**, l’intégration **Lambda**, le support **WebSocket**, etc.
- **NLB** : pour les **applications à faible latence**, **trafic important**, ou protocoles non HTTP.

## Server Name Indication (SNI)
- **SNI** permet à **plusieurs sites web sécurisés (HTTPS)** de **partager la même adresse IP** et le même port (443)  
- C’est utile quand tu as **plusieurs noms de domaine** sur le **même serveur**

### 🔍 Comment ça marche ?

- Normalement, dans une connexion HTTPS, le serveur doit envoyer son **certificat SSL** *avant* de savoir à quel site tu veux accéder  
- Avec **SNI**, le **navigateur indique le nom du site (le hostname)** dès le début de la connexion  
- Le serveur peut alors **choisir le bon certificat SSL** en fonction du nom demandé


### 📦 Exemple simple

Tu as une seule IP pour deux sites :  
- `www.maboutique.com`  
- `www.mablog.com`

Grâce à **SNI**, tu peux :  
- **utiliser un certificat différent pour chaque site**  
- **les faire coexister sur une même IP et un seul Load Balancer**
### ✅ Pourquoi c’est important en AWS ?
- **Application Load Balancer (ALB)** utilise **SNI** pour gérer **plusieurs certificats SSL (ACM)** sur un seul listener  
- Permet de **faire du HTTPS multi-domaines facilement**, sans devoir acheter plusieurs IP ou ALBs


## Scaling Policies

| **Type de Scaling Policy**  | **Principe**                                                                 | **Déclencheur**                         | **Avantages**                                         | **Limitations**                                      | **Cas d’usage**                                               |
|-----------------------------|------------------------------------------------------------------------------|----------------------------------------|--------------------------------------------------------|------------------------------------------------------|---------------------------------------------------------------|
| **Target Tracking**         | Ajuste le nombre d’instances pour atteindre une **valeur cible** (ex : CPU) | Métrique CloudWatch (ex. CPU, ALB...) | Simple à configurer, ajustement automatique            | Pas de contrôle précis sur le nb d’instances ajoutées | Maintenir un seuil de CPU, de requêtes ALB, etc.              |
| **Step Scaling**            | Exécute **différentes actions** selon l’**écart** par rapport à un seuil    | Alarme CloudWatch                      | Contrôle fin des actions selon plusieurs paliers       | Plus complexe à configurer que Target Tracking       | Réagir différemment selon des niveaux de charge               |
| **Simple Scaling** *(legacy)* | Lance une **action unique** suite à une alarme CloudWatch                  | Alarme CloudWatch                      | Facile à comprendre                                     | Doit attendre un cooldown avant de se relancer       | Ancien comportement, à éviter pour les nouveaux déploiements  |
| **Scheduled Scaling**       | Change la capacité à des **horaires définis**                              | Planification                          | Prévisible, idéal pour les charges connues             | Ne réagit pas aux pics inattendus                    | Appliquer une échelle fixe pendant les heures de bureau        |
| **Predictive Scaling**      | **Anticipe la charge future** grâce au machine learning                     | Historique de métriques                | Provisionne **en avance**, réduit les temps de montée | Besoin de données historiques / charge prévisible    | Événements récurrents, horaires fixes, pics réguliers          |

---

Souhaites-tu que je te génère un schéma ou une mindmap pour visualiser tout ça rapidement ?

