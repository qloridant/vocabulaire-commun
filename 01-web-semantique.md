# Explication — Le Web sémantique : pourquoi et comment

> Cette page explique les **concepts fondamentaux** du Web sémantique. Elle ne vous demande pas de coder, elle vous aide à comprendre.

---

## Le problème que résout le Web sémantique

Les administrations françaises produisent d'immenses volumes de données : fichiers CSV, bases SQL, API JSON, PDF… Ces données sont souvent **identiques dans leur sens** mais **incompatibles dans leur forme**.

Exemple :

| Source | Champ | Valeur |
|---|---|---|
| DGFIP | `commune` | `"Paris"` |
| INSEE | `lib_commune` | `"PARIS"` |
| Mairie de Paris | `city` | `"Paris 4e"` |
| Commission EU | `adminUnit` | `"FR-75"` |

Ces quatre valeurs désignent **la même réalité**, mais aucun système ne peut le déduire automatiquement.

Le Web sémantique résout ce problème en donnant aux données une **signification formelle et partagée**, compréhensible par les machines.

---

## Les trois piliers

### 1. Les URIs — donner une identité unique à chaque chose

Sur le Web, chaque page a une URL. Le Web sémantique étend ce principe : **chaque concept, chaque entité du monde réel** peut avoir une URI.

```
https://data.example.gouv.fr/commune/75056
↑ Ce n'est plus juste une page. C'est l'identité de la commune de Paris.
```

### 2. RDF — décrire les relations entre les choses

**RDF (Resource Description Framework)** permet d'exprimer des faits sous forme de triplets :

```
(Paris) → (est la capitale de) → (France)
(Paris) → (a pour code INSEE) → ("75056")
(Paris) → (est identique à) → (https://www.wikidata.org/entity/Q90)
```

Ces triplets forment un **graphe de données**, liables à d'autres graphes.

### 3. Les vocabulaires — parler le même langage

Un vocabulaire définit les **classes** et les **propriétés** à utiliser pour décrire un domaine.

Sans vocabulaire commun :
- Service A dit `ville`
- Service B dit `commune`
- Service C dit `city`

Avec le **Core Location Vocabulary** :
- Tous disent `locn:postName`

---

## Pourquoi les Core Vocabularies de l'UE ?

La Commission européenne, via le programme **ISA²/SEMIC**, a développé des vocabulaires minimalistes, stables et réutilisables pour décrire :

- des **personnes** (Core Person)
- des **organisations** (Core Business)
- des **lieux** (Core Location)
- des **services publics** (CPSV-AP)
- des **critères et preuves** (CCCEV)

Ces vocabulaires sont :

- **Légers** — peu de classes, peu de propriétés obligatoires
- **Extensibles** — conçus pour être enrichis par des profils nationaux
- **Alignés** avec schema.org, DCAT, FOAF, Dublin Core
- **Gratuits et ouverts**

---

## Le lien avec les obligations légales

| Obligation | Lien avec le Web sémantique |
|---|---|
| **RGAA** (accessibilité) | Les métadonnées structurées aident les technologies d'assistance |
| **Loi Lemaire** (open data) | DCAT-AP (basé sur RDF) est le standard de catalogage |
| **Règlement INSPIRE** | Exige des métadonnées géographiques en RDF |
| **EUCS / Espace européen des données** | Interopérabilité sémantique requise |
| **Data Governance Act** | Encourage les espaces de données avec métadonnées standard |

---

## Ce que le Web sémantique n'est pas

| Idée reçue | Réalité |
|---|---|
| "C'est réservé aux chercheurs" | Les Core Vocabularies sont conçus pour l'administration |
| "Il faut tout réécrire" | JSON-LD permet d'enrichir des APIs existantes |
| "C'est trop complexe" | Commencer par décrire 5 propriétés suffit pour démarrer |
| "Personne ne l'utilise" | schema.org est utilisé par Google, Bing, data.gouv.fr… |

---

## Pour aller plus loin

- [Explication — RDF et les graphes de données](./02-rdf-graphes.md)
- [Explication — Les formats : Turtle, JSON-LD, RDF/XML](./03-formats.md)
- [Tutoriel 01 — Mon premier triplet RDF](../tutoriels/01-core-person.md)
