# Fiche d'analyse du jeu de données

## Identification

- **Groupe** : EL AKKIOUI & BELLIL
- **Date** : 19 Avril 2026
- **Nom du jeu de données** : Etablissements de l'enseignement supérieur universitaire public ouverts au titre de l'année 2024-2025
- **Source / producteur** : MESRSI (Ministère de l'Enseignement Supérieur, de la Recherche Scientifique et de l'Innovation)
- **URL de la source** : https://data.gov.ma/data/dataset/etablissements-de-l-enseignement-superieur-universitaire-public-ouverts/
- **Domaine métier** : Enseignement supérieur, éducation publique

## Description générale

- **Objectif du jeu de données** : Recenser les établissements universitaires publics marocains en activité pour l'année 2024-2025.
- **Format(s) disponible(s)** : XLSX (original sur portail), CSV (fichier pédagogique fourni).
- **Langue(s)** : Français (avec potentiellement des équivalents arabes sur le portail complet).
- **Licence** : ODbL (Open Database License).
- **Fréquence de mise à jour** : Annuelle (dernière maj 19 février 2025).
- **Unité d'observation** : Un établissement universitaire (école, faculté, institut).
- **Granularité des enregistrements** : L'entité physique/administrative locale (ex: ENSIAS à Rabat).

## Structure du jeu de données

| Champ / colonne | Signification | Exemple de valeur | Observation |
| --- | --- | --- | --- |
| `institution_name` | Nom complet de l'établissement | Ecole Nationale Superieure d'Informatique et d'Analyse des Systemes | Risque de variations typographiques (accents absents). |
| `short_name` | Acronyme ou abréviation | ENSIAS | Utile pour la recherche textuelle, mais risque d'homonymie (ex: plusieurs ENSA). |
| `city` | Ville d'implantation de l'établissement | Rabat | Élément spatial clé, texte libre nécessitant un alignement géographique. |
| `website` | URL du site officiel | https://www.ensias.um5.ac.ma | Lien web documentaire, non sémantique. |
| `institution_type` | Catégorie administrative de la structure | public school | Typologie utile pour le filtrage ontologique. |
| `main_field` | Domaine disciplinaire principal | computer science | Terminologie non contrôlée par un thésaurus explicite. |

## Évaluation "Open Data"

| Critère | Observation | Score / 5 |
| --- | --- | --- |
| Accessibilité | Téléchargement direct sans barrière d'authentification sur le portail data.gov.ma. | 5/5 |
| Réutilisabilité | Licence ODbL explicite, autorisant la modification et le croisement. | 5/5 |
| Documentation | Métadonnées basiques présentes, mais dictionnaire de données détaillé absent. | 3/5 |
| Format exploitable | CSV disponible (via échantillon), format tabulaire propre. Le XLSX d'origine limite l'automatisation directe. | 4/5 |
| Présence d'une licence | Spécifiée clairement sur le portail (ODbL). | 5/5 |

## Évaluation "Linked Data readiness"

| Point observé | Oui / Non / Partiel | Commentaire |
| --- | --- | --- |
| Identifiants stables pour les enregistrements | Non | Le champ `record_id` est séquentiel et local au fichier, non résolvable sur le web. |
| Entités clairement distinguables | Oui | Séparation logique entre établissement, ville et domaine. |
| Valeurs normalisées | Partiel | Noms et types présents mais sous forme de chaînes de caractères brutes. |
| Informations géographiques exploitables | Partiel | Nom de la ville présent, mais absence de coordonnées géospatiales (lat/long) ou de code région. |
| Possibilité de lien vers des référentiels externes | Oui | Les villes (GeoNames) et les établissements (Wikidata/DBpedia) sont hautement connectables. |
| Présence de clés candidates plausibles | Oui | Le tuple (`short_name`, `city`) forme une clé candidate robuste. |
| Présence de codes ou nomenclatures réutilisables | Non | Absence de code établissement officiel (ex: code Apogée national ou équivalent UAI). |

## Maturité "5 étoiles"

| Niveau | Atteint ? | Justification |
| --- | --- | --- |
| 1 étoile : données disponibles sur le Web | Oui | Fichiers exposés sur data.gov.ma avec licence ouverte. |
| 2 étoiles : données structurées | Oui | Données formatées en grille tabulaire lisible par machine (XLSX/CSV). |
| 3 étoiles : format non propriétaire | Oui | Conversion possible et fourniture d'un fichier CSV. |
| 4 étoiles : usage d'URI pour identifier les choses | Non | Les établissements et villes sont de simples chaînes de texte (`"ENSIAS"`, `"Rabat"`). |
| 5 étoiles : liens vers d'autres données | Non | Graphe isolé, aucune relation RDF (`owl:sameAs`, etc.) n'est établie. |

## Clés candidates et identifiants

| Objet ou entité cible | Champ(s) candidat(s) | Fiabilité | Limites | Recommandation |
| --- | --- | --- | --- | --- |
| Établissement | `short_name` + `city` | Haute | Les acronymes peuvent muter ou être partagés si l'université mère change. | Générer une URI déterministe basée sur le slug du nom court et de la ville. |
| Ville | `city` | Moyenne | Homonymies possibles sans contexte régional. Fautes de frappe. | Remplacer la chaîne par l'identifiant numérique GeoNames (ex: Rabat -> 2538475). |
| Domaine d'étude | `main_field` | Faible | Vocabulaire libre sujet aux variations sémantiques. | Aligner les termes sur un vocabulaire SKOS ou Wikidata (ex: Q21198 pour Informatique). |

## Normalisation préalable nécessaire

| Champ | Problème observé | Impact pour le LOD | Action recommandée |
| --- | --- | --- | --- |
| `institution_name` | Absence d'accents (ex: "Ecole Nationale Superieure"). | Complexifie la réconciliation lexicale avec des bases externes standardisées. | Appliquer des fonctions de nettoyage et de standardisation Unicode. |
| `city` | Variabilité orthographique (ex: Fes vs Fès, Marrakesh vs Marrakech). | Fragmentation des nœuds spatiaux lors de la construction du graphe. | Utiliser un algorithme de rapprochement (ex: Levenshtein) pour s'aligner sur un référentiel géographique. |
| `institution_type` | Chaînes de texte disparates (ex: "public school", "engineering school"). | Empêche le typage strict via `rdf:type`. | Mettre en place un dictionnaire de correspondance vers une ontologie (ex: schema:CollegeOrUniversity). |

## Qualité des données

- **Forces observées** : Dataset officiel, couverture exhaustive du territoire, colonnes bien délimitées, granularité pertinente.
- **Faiblesses observées** : Typage de données exclusivement textuel, absence totale de clés étrangères ou de codes identifiants de l'administration marocaine.
- **Ambiguïtés ou doublons potentiels** : Risque d'amalgame sur les réseaux d'écoles (ENSA, EST) si le filtre géographique n'est pas appliqué strictement lors de la transformation.
- **Champs manquants pour une meilleure interconnexion** : Coordonnées spatiales (latitude, longitude), identifiant unique national, identifiant de l'université de tutelle structuré en colonne séparée.
- **Risque principal pour une future mise en relation** : La dépendance aux labels textuels pour forger les futures URIs, rendant le graphe fragile face aux modifications de nomenclature des établissements.

## Conclusion courte

Ce jeu de données du MESRSI possède une maturité Open Data de niveau 3 étoiles, offrant une excellente base tabulaire pour le recensement des infrastructures académiques. Cependant, pour atteindre le niveau Linked Open Data (4-5 étoiles), une ingénierie de données ciblée est requise. L'absence d'identifiants nationaux impose la création d'URIs customisées (`http://data.gov.ma/resource/etablissement/...`) basées sur des clés composites (nom/ville). La priorité technique consistera à normaliser les toponymes pour les aligner sur GeoNames et à désambiguïser les acronymes académiques pour permettre des liaisons sémantiques précises avec Wikidata et DBpedia.