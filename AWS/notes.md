# AWS Notes

## IAM
- **Policies** : Pour Users et Groupes
- **Roles** Pour Services

## EC2
### Option achat
- **On demand** : Arrive quand on veut à l'hôtel, mais on paie plein tarif.
- **Reserved** : Réserve à l'avance pour une longue durée et on obtient une réduction.
- **Savings Plans** : Paie un montant fixe par heure pendant une certaine durée, peu importe le type de chambre.
- **Instances Spot** : OMse sur les chambres vides à petit prix, l'hôtel peut nous demander de partir à tout moment.
**Dedicated host** : Réserve tout un bâtiment de l'hôtel.
- **Capacity reservations** : Réserve une chambre à plein tarif pour une durée déterminée, même si on ne l'utilise pas.

### Placement Group
- **Cluster** : Groupe d'instances dans une même AZ pour faible latence.
- **Spread** : Instances réparties sur différents matériels (max 7 par AZ).
- **Partition** : Instances réparties par partitions indépendantes (jusqu'à des centaines par AZ, idéal pour Hadoop, Cassandra, Kafka).