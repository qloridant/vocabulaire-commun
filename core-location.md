# Référence — Core Location Vocabulary

**Namespace** : `http://www.w3.org/ns/locn#`  
**Préfixe courant** : `locn:`  
**Spécification** : [https://www.w3.org/ns/locn](https://www.w3.org/ns/locn)  
**Mainteneur** : W3C / Commission européenne (SEMIC)

---

## Classes

| Classe | Description |
|---|---|
| `locn:Address` | Adresse postale structurée |
| `locn:Geometry` | Représentation géométrique (point, ligne, polygone) |

## Propriétés de liaison

| Propriété | Domaine | Description |
|---|---|---|
| `locn:location` | Toute ressource | Lie une ressource à un lieu (`dct:Location`) |
| `locn:address` | Toute ressource | Lie une ressource à une adresse |
| `locn:geometry` | `dct:Location` | Lie un lieu à sa géométrie |

---

## Propriétés de `locn:Address`

| Propriété | Type | Description |
|---|---|---|
| `locn:fullAddress` | `rdfs:Literal` | Adresse complète en texte libre |
| `locn:thoroughfare` | `rdfs:Literal` | Voie (nom de rue, avenue…) |
| `locn:locatorDesignator` | `rdfs:Literal` | Numéro dans la voie |
| `locn:locatorName` | `rdfs:Literal` | Nom du bâtiment ou du point de repère |
| `locn:addressArea` | `rdfs:Literal` | Quartier, lieu-dit |
| `locn:postName` | `rdfs:Literal` | Commune |
| `locn:postCode` | `rdfs:Literal` | Code postal |
| `locn:adminUnitL1` | `rdfs:Literal` | Pays |
| `locn:adminUnitL2` | `rdfs:Literal` | Région ou département |

---

## Exemple complet

```turtle
@prefix locn:       <http://www.w3.org/ns/locn#> .
@prefix geosparql:  <http://www.opengis.net/ont/geosparql#> .
@prefix geo:        <http://www.w3.org/2003/01/geo/wgs84_pos#> .
@prefix xsd:        <http://www.w3.org/2001/XMLSchema#> .
@prefix dct:        <http://purl.org/dc/terms/> .

<https://data.example.gouv.fr/adresse/hotel-de-ville-lyon>
    a locn:Address ;

    locn:fullAddress       "1 Place de la Comédie, 69001 Lyon, France"@fr ;
    locn:thoroughfare      "Place de la Comédie" ;
    locn:locatorDesignator "1" ;
    locn:postCode          "69001" ;
    locn:postName          "Lyon"@fr ;
    locn:adminUnitL1       "France"@fr ;
    locn:adminUnitL2       "Auvergne-Rhône-Alpes"@fr .

<https://data.example.gouv.fr/lieu/hotel-de-ville-lyon>
    a dct:Location ;
    locn:address   <https://data.example.gouv.fr/adresse/hotel-de-ville-lyon> ;
    locn:geometry  [
        a locn:Geometry ;
        geosparql:asWKT "POINT(4.8344 45.7676)"^^geosparql:wktLiteral ;
        geo:lat  "45.7676"^^xsd:decimal ;
        geo:long "4.8344"^^xsd:decimal
    ] .
```

---

## Formats de géométrie supportés

| Format | Type RDF |
|---|---|
| WKT (OGC) | `geosparql:wktLiteral` |
| GML | `geosparql:gmlLiteral` |
| GeoJSON | `geosparql:geoJSONLiteral` |
| Lat/Lon WGS84 | `geo:lat` + `geo:long` |

---

## Intégration avec la BAN

```turtle
@prefix ban:  <https://adresse.data.gouv.fr/base-adresse-nationale/> .
@prefix owl:  <http://www.w3.org/2002/07/owl#> .

<https://data.example.gouv.fr/adresse/hotel-de-ville-lyon>
    owl:sameAs ban:69123_0380_00001 .
```

---

## Voir aussi

- [Tutoriel 03 — Localiser avec Core Location](../tutoriels/03-core-location.md)
- [API Base Adresse Nationale](https://api-adresse.data.gouv.fr)
- [GeoSPARQL Specification](https://www.ogc.org/standard/geosparql/)
