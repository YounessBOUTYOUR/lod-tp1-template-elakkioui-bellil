# Benchmark des référentiels externes

## Identification
- **Groupe** : EL AKKIOUI & BELLIL
- **Membres** : Aymane EL AKKIOUI, Zakaria BELLIL
- **Jeu de données étudié** : Établissements de l'enseignement supérieur universitaire public (2024-2025) - MESRSI (data.gov.ma)
- **Types d'entités retenus** : Établissement d'enseignement supérieur, Ville, Région/Académie.

## 1. Référentiels comparés

| Référentiel | URL | Types d'entités couverts | Intérêt potentiel | Limites observées |
| --- | --- | --- | --- | --- |
| Wikidata | `https://www.wikidata.org` | Établissements, Villes, Réseaux (ex: ENSA) | URIs stables, graphe riche, requêtable via SPARQL, gestion du multilinguisme (Fr/Ar). | Granularité parfois inégale (facultés spécifiques manquantes), ambiguïté des acronymes. |
| GeoNames | `https://www.geonames.org` | Villes, Régions | Standard mondial pour l'information spatiale, coordonnées GPS (WGS84) précises. | Inadapté pour les entités non géographiques (établissements). |
| DBpedia | `https://dbpedia.org` | Établissements majeurs (Universités) | Alignement direct avec les infoboxes Wikipedia, typage ontologique fort (`dbo:University`). | Données obsolètes (anciennes structurations des universités marocaines), faible couverture des instituts spécialisés. |

## 2. Critères de comparaison

- **Couverture spatiale et institutionnelle** : Capacité à référencer les universités marocaines et leurs composantes régionales.
- **Résolution d'entité (Entity Resolution)** : Capacité à distinguer un réseau (ex: "Réseau ENSA") d'une instance spécifique (ex: "ENSA Kénitra").
- **Interopérabilité sémantique** : Disponibilité de classes formelles (`wd:Q3918` pour université) et de liens d'équivalence (`owl:sameAs`).
- **Persistance** : Stabilité des identifiants (Q-nodes de Wikidata vs URL changeantes).

## 3. Cas d'appariement manuel

| Entité locale | Type | Référentiel cible | Correspondance candidate | Indices utilisés | Niveau de confiance | Décision |
| --- | --- | --- | --- | --- | --- | --- |
| Université Mohammed V | Établissement | Wikidata | `wd:Q1541823` | Nom exact + Localisation (Rabat) + Type (Q3918) | Élevé | Accepté |
| ENSIAS | Établissement | Wikidata | `wd:Q3577884` | Acronyme + Appartenance (UM5) | Élevé | Accepté |
| Cadi Ayyad University | Établissement | DBpedia | `dbr:Cadi_Ayyad_University` | Correspondance lexicale stricte | Moyen | Accepté |
| Rabat | Ville | GeoNames | `2538475` | Toponymie + Hiérarchie administrative (Maroc) | Élevé | Accepté |
| Sciences de l'ingénieur | Domaine | Wikidata | `wd:Q11023` | Correspondance sémantique (Engineering) | Moyen | À valider |
| Région Fès-Meknès | Région | GeoNames | `8267073` | Nomenclature territoriale officielle | Élevé | Accepté |

## 4. Synthèse
Wikidata s'impose comme le pivot principal d'alignement pour les entités institutionnelles (Universités, Écoles) grâce à son typage riche et sa modélisation hiérarchique (propriété `P361` "part of" pour lier une faculté à son université). GeoNames est strictement assigné à la réconciliation des entités spatiales (Villes, Régions). DBpedia servira de fallback pour l'extraction de résumés textuels (abstracts) des grandes universités.