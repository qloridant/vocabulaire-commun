# Documentation Web Sémantique — Administrations françaises

> Documentation à destination des agents techniques des administrations françaises pour l'adoption du Web sémantique et des vocabulaires Core de l'Union européenne.

---

## Structure de la documentation

Cette documentation suit le cadre **Diátaxis** :

| Section | Objectif | Pour qui |
|---|---|---|
| [Tutoriels](./tutoriels/) | Apprendre par la pratique | Débutants |
| [Guides pratiques](./guides/) | Résoudre un problème concret | Praticiens |
| [Références](./references/) | Consulter une spec | Développeurs |
| [Explications](./explication/) | Comprendre les concepts | Tous |

---

## Pourquoi le Web sémantique ?

Les administrations françaises publient des données hétérogènes, fragmentées, souvent incomparables entre services. Le Web sémantique, via des **vocabulaires standardisés**, permet :

- l'**interopérabilité** entre systèmes (DINUM, collectivités, Europe)
- la **réutilisation** des données publiques (open data)
- l'**alignement avec les exigences européennes** (INSPIRE, ISA², SEMIC)

---

## Vocabulaires prioritaires (Core Vocabularies UE)

Les **Core Vocabularies** sont des vocabulaires RDF légers, maintenus par la Commission européenne via le programme **SEMIC** :

| Vocabulaire | Domaine | Namespace |
|---|---|---|
| [Core Person Vocabulary](./references/core-person.md) | Personnes physiques | `http://www.w3.org/ns/person#` |
| [Core Business Vocabulary](./references/core-business.md) | Organisations, entreprises | `http://www.w3.org/ns/legal#` |
| [Core Location Vocabulary](./references/core-location.md) | Adresses, géographie | `http://www.w3.org/ns/locn#` |
| [Core Public Service Vocabulary](./references/core-public-service.md) | Services publics | `http://purl.org/vocab/cpsv#` |
| [Core Criterion & Evidence Vocabulary](./references/core-criterion.md) | Critères, pièces justificatives | `http://data.europa.eu/m8g/` |

---

## Prérequis techniques

- Notions de base en **XML/JSON**
- Connaissance des **API REST**
- Optionnel : bases de **RDF/Turtle**

---

## Liens utiles

- [SEMIC — Semantic Interoperability Community](https://joinup.ec.europa.eu/collection/semantic-interoperability-community-semic)
- [data.europa.eu](https://data.europa.eu)
- [schema.gouv.fr](https://schema.gouv.fr)
- [api.gouv.fr](https://api.gouv.fr)
- [W3C RDF Primer (FR)](https://www.w3.org/TR/rdf11-primer/)
