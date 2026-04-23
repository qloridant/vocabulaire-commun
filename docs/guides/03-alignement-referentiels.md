# Guide — Aligner ses données avec les référentiels européens

> **Problème** : Vos données utilisent des codes internes (INSEE, SIRET…). Comment les rendre interopérables avec les systèmes européens ?  
> **Solution** : Utiliser `owl:sameAs` et les vocabulaires de correspondance.

---

## 1. Le principe d'alignement

L'alignement consiste à déclarer qu'une ressource locale **est identique** à une ressource d'un référentiel externe. Cela permet aux moteurs de raisonnement de fusionner les informations.

```turtle
@prefix owl: <http://www.w3.org/2002/07/owl#> .

# Ma ressource locale
<https://data.example.gouv.fr/commune/75056>
    owl:sameAs <https://sws.geonames.org/2988507/> ;           # GeoNames
    owl:sameAs <http://dbpedia.org/resource/Paris> ;           # DBpedia
    owl:sameAs <https://www.wikidata.org/entity/Q90> .         # Wikidata
```

---

## 2. Aligner avec le référentiel des pays EU

Le **Publications Office** maintient un référentiel des pays, langues, et types d'organisations :

```turtle
@prefix dct:  <http://purl.org/dc/terms/> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .

<https://data.example.gouv.fr/service/immatriculation>
    dct:language <http://publications.europa.eu/resource/authority/language/FRA> ;
    dct:spatial  <http://publications.europa.eu/resource/authority/country/FRA> .
```

Référentiels disponibles sur [https://op.europa.eu/en/web/eu-vocabularies](https://op.europa.eu/en/web/eu-vocabularies) :

| Référentiel | Namespace |
|---|---|
| Pays | `http://publications.europa.eu/resource/authority/country/` |
| Langues | `http://publications.europa.eu/resource/authority/language/` |
| Types de fichiers | `http://publications.europa.eu/resource/authority/file-type/` |
| Fréquences | `http://publications.europa.eu/resource/authority/frequency/` |

---

## 3. Aligner les types d'organisations

```turtle
@prefix legal: <http://www.w3.org/ns/legal#> .
@prefix org:   <http://www.w3.org/ns/org#> .

<https://data.example.gouv.fr/org/prefecture-69>
    a legal:LegalEntity ;
    # Type d'organisation selon le référentiel EU
    legal:companyType
        <http://publications.europa.eu/resource/authority/legal-entity-type/GOV> ;
    # Niveau administratif NUTS
    org:classification
        <http://data.europa.eu/nuts/code/FRK> .  # Auvergne-Rhône-Alpes
```

---

## 4. Aligner les communes avec GeoNames

```turtle
@prefix locn: <http://www.w3.org/ns/locn#> .
@prefix owl:  <http://www.w3.org/2002/07/owl#> .
@prefix dct:  <http://purl.org/dc/terms/> .

<https://data.example.gouv.fr/commune/69123>
    a locn:Address ;
    locn:postName   "Lyon"@fr ;
    locn:postCode   "69001" ;
    dct:identifier  "69123" ;       # Code INSEE
    owl:sameAs      <https://sws.geonames.org/2996944/> .
```

---

## 5. Vérifier un alignement avec SPARQL

```sparql
PREFIX owl:  <http://www.w3.org/2002/07/owl#>
PREFIX locn: <http://www.w3.org/ns/locn#>

SELECT ?commune ?uri_externe
WHERE {
  ?commune a locn:Address ;
           owl:sameAs ?uri_externe .
  FILTER(STRSTARTS(STR(?uri_externe), "https://sws.geonames.org"))
}
```

---

## Bonnes pratiques

- Préférez `owl:sameAs` pour les **identités exactes**
- Utilisez `skos:closeMatch` ou `skos:exactMatch` pour les **concepts proches**
- Ne créez pas de nouveaux identifiants si un référentiel officiel existe déjà
- Documentez la source et la date de vos alignements

---

## Étape suivante

→ [Référence — Core Person Vocabulary](../references/core-person.md)
