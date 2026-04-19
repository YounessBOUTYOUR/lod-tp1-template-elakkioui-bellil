# Plan de normalisation et d'identification

## 1. Entités prioritaires

| Type d'entité | Pourquoi est-il prioritaire ? | Identifiant actuel | Stratégie proposée |
| --- | --- | --- | --- |
| Établissement | Nœud central du graphe cible | `id` séquentiel / Aucun | Génération d'URI canoniques basées sur l'acronyme et la ville. |
| Ville | Point d'ancrage spatial | Chaîne de caractères | Remplacement par l'URI GeoNames (`sws.geonames.org/{id}`). |
| Réseau universitaire | Regroupement logique (ex: UH2C) | Implicite dans le nom | Création d'entités parentes liant les sous-établissements. |

## 2. Champs à normaliser

| Champ | Problème observé | Règle de normalisation proposée | Impact attendu |
| --- | --- | --- | --- |
| `Etablissement` | Casse incohérente, présence de caractères spéciaux | `TRIM()`, `LOWER()`, suppression des accents, encodage UTF-8 | Fiabilisation des jointures textuelles. |
| `Type_Etablissement` | Vocabulaire libre et varié | Mapping vers un thésaurus contrôlé (`skos:ConceptScheme`) | Harmonisation catégorielle (Faculté, École, Institut). |
| `Ville` | Variations orthographiques (ex: Meknes / Meknès) | Rapprochement de chaînes (Levenshtein) sur référentiel | Alignement GeoNames à 100%. |
| `Universite_de_tutelle` | Cellules fusionnées ou vides | Imputation par héritage, normalisation du nom de la tutelle | Reconstruction de l'arbre de dépendance institutionnelle. |

## 3. Stratégie d'identifiants

| Entité cible | Base de construction envisagée | Risque | Recommandation |
| --- | --- | --- | --- |
| Établissement | `http://data.gov.ma/resource/Etablissement/{slugify(acronyme_ville)}` | Collision si plusieurs instituts ont le même acronyme dans la même ville | Adjonction d'un hash court ou du code de tutelle en cas de conflit. |
| Ville | `https://sws.geonames.org/{id}/` | Dépendance à un endpoint externe | Stratégie robuste (GeoNames est stable). |
| Domaine académique | `http://data.gov.ma/resource/Domaine/{code_interne}` | Absence de norme nationale unifiée | Création d'un dictionnaire local mappé vers `wd:` via `skos:exactMatch`. |

## 4. Risques et limites
- **Désambiguïsation des acronymes** : Des entités comme "EST" ou "ENSA" nécessitent systématiquement le contexte spatial (`Ville`) pour lever l'ambiguïté.
- **Absence de clés naturelles** : Le dataset originel manque de codes officiels de l'éducation nationale (équivalent UAI en France), forçant la création d'URI ad-hoc basées sur des heuristiques textuelles.
- **Évolution temporelle** : Les changements de statut ou fusions d'universités (ex: création de l'UM5 issue de UM5A et UM5S) ne sont pas tracés temporellement.

---

## Conclusion

Le dataset nécessite une normalisation préalable importante avant une transformation en LOD.  
Les priorités sont :
- création d’identifiants stables
- alignement sur des référentiels externes
- nettoyage des valeurs textuelles

Ces étapes permettront une future modélisation RDF cohérente.