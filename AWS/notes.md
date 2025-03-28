# AWS Notes

## IAM
- **Policies** : Pour Users et Groupes
- **Roles** : Pour Services

---
## EC2
### Option achat
- **On demand** : Arrive quand on veut à l'hôtel, mais on paie plein tarif.
- **Reserved** : Réserve à l'avance pour une longue durée et on obtient une réduction.
- **Savings Plans** : Paie un montant fixe par heure pendant une certaine durée, peu importe le type de chambre.
- **Instances Spot** : Prends sur les chambres vides à petit prix, l'hôtel peut nous demander de partir à tout moment.
- **Dedicated host** : Réserve tout un bâtiment de l'hôtel.
- **Capacity reservations** : Réserve une chambre à plein tarif pour une durée déterminée, même si on ne l'utilise pas.

### Placement Group
- **Cluster** : Groupe d'instances dans une même AZ pour faible latence.
- **Spread** : Instances réparties sur différents matériels (max 7 par AZ).
- **Partition** : Instances réparties par partitions indépendantes (jusqu'à des centaines par AZ, idéal pour Hadoop, Cassandra, Kafka).

---
## High availabity & Scalabilty
⚠️ GENEVE protocol port 6081 => Gateway Load Balancer

---
## RDS + Aurora + ElastiCache
⚠️ **Read Replicas** peuvent etre setup en **Multi AZ for Disaster Recovery**

### RDS Backup
Si RDS DB stoppée pendant longtemps, prendre un snap et le restaurer (car RDS facture meme stoppée et snapshot coute moins cher).


### **Résumé rapide :**

| Critère            | RDS                      | Aurora                     | ElastiCache                 |
|--------------------|--------------------------|----------------------------|-----------------------------|
| Type               | Relationnel classique ✅ | Relationnel haute perf ✅  | Cache (clé-valeur) ✅       |
| Performance        | Bonne ⚠️                 | Excellente ✅              | Ultra-élevée (RAM) ✅       |
| Scalabilité        | Verticale ⚠️             | Horizontale (réplicas) ✅  | Horizontale (nœuds) ✅      |
| Persistance        | Élevée ✅                | Très élevée ✅             | Faible (en mémoire) ⚠️      |
| Latence            | Moyenne ⚠️               | Faible ✅                  | Ultra-faible ✅             |


### 🎯 **En bref :**
- **RDS** : Base relationnelle traditionnelle, facile à gérer.
- **Aurora** : Performances supérieures, fiabilité maximale.
- **ElastiCache** : Accélère l’accès aux données en mémoire, ultra-rapide.

---
## Stockage

| Solution        | Analogie simple                                      | Utilisation typique      |
|-----------------|------------------------------------------------------|--------------------------|
| **S3**          | Grand coffre-fort en ligne                           | Stockage durable, partage|
| **EBS**         | Disque dur externe branché sur un ordinateur         | Stockage persistant      |
| **EFS**         | Disque réseau partagé par plusieurs ordinateurs      | Partage facile de fichiers|
| **Instance Store**| Clé USB très rapide, mais temporaire               | Données temporaires rapides|

### S3 : Sécurité


#### **Résumé rapide des méthodes :**

| Méthode         | Gestion des clés    | Facilité            | Contrôle clés        | Niveau sécurité |
|-----------------|---------------------|---------------------|----------------------|-----------------|
| **SSE-S3**      | AWS                 | ✅ Très facile       | ⚠️ Faible            | Élevé           |
| **SSE-KMS**     | AWS KMS             | ✅ Facile à moyen    | ✅ Élevé             | Très élevé      |
| **SSE-C**       | Client              | ⚠️ Complexe         | ✅ Très élevé        | Très élevé      |
| **Client-Side** | Client ou AWS KMS   | ⚠️ Complexe         | ✅ Très élevé        | Très élevé      |

- **SSE-S3** : simplicité maximale.
- **SSE-KMS** : équilibre simplicité/contrôle.
- **SSE-C / Client-side** : contrôle total, mais gestion complexe.

