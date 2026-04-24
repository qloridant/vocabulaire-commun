# Tutoriel 05 — Charger et interroger ses données avec Oxigraph

> **Objectif** : Installer Oxigraph, charger un fichier Turtle et exécuter ses premières requêtes SPARQL en local.  
> **Durée estimée** : 45 minutes  
> **Niveau** : Intermédiaire

---

## Ce que vous allez apprendre

- Installer Oxigraph en local (binaire ou Docker)
- Charger un fichier RDF/Turtle
- Exécuter des requêtes SPARQL via l'interface web et via `curl`
- Comprendre les graphes nommés

---

## 1. Qu'est-ce qu'Oxigraph ?

**Oxigraph** est un triplestore RDF open source, écrit en Rust, léger et sans dépendances. Il expose :

- un **endpoint SPARQL** (requêtes SELECT, CONSTRUCT, ASK, UPDATE)
- une **interface web** intégrée pour tester des requêtes
- une **API HTTP** compatible avec le protocole SPARQL 1.1

C'est un choix adapté pour les administrations : simple à déployer, sans base de données externe, performant sur des volumes de quelques millions de triplets.

---

## 2. Installation

=== "Binaire (Linux/macOS)"

    ```bash
    # Télécharger la dernière release
    curl -L https://github.com/oxigraph/oxigraph/releases/latest/download/oxigraph_linux_x86_64 \
      -o oxigraph
    chmod +x oxigraph

    # Vérifier
    ./oxigraph --version
    ```

=== "Docker"

    ```bash
    docker pull ghcr.io/oxigraph/oxigraph

    # Lancer avec un volume persistant
    docker run --rm \
      -v $PWD/data:/data \
      -p 7878:7878 \
      ghcr.io/oxigraph/oxigraph \
      serve --location /data --bind 0.0.0.0:7878
    ```

=== "Cargo (Rust)"

    ```bash
    cargo install oxigraph
    ```

---

## 3. Démarrer le serveur

```bash
# Créer un dossier de stockage
mkdir -p ./oxigraph-data

# Lancer le serveur
./oxigraph serve --location ./oxigraph-data --bind 0.0.0.0:7878
```

Le serveur est disponible sur **http://localhost:7878**.

Ouvrez votre navigateur : vous accédez à l'interface SPARQL interactive.

---

## 4. Préparer un fichier Turtle

Créez un fichier `organisations.ttl` :

```turtle
@prefix legal:  <http://www.w3.org/ns/legal#> .
@prefix org:    <http://www.w3.org/ns/org#> .
@prefix schema: <http://schema.org/> .
@prefix locn:   <http://www.w3.org/ns/locn#> .

<https://data.example.gouv.fr/org/dinum>
    a legal:LegalEntity, org:Organization ;
    legal:legalName      "Direction interministérielle du numérique"@fr ;
    legal:alternativeName "DINUM"@fr ;
    legal:legalIdentifier "13001518700013" ;
    schema:url           <https://www.numerique.gouv.fr> .

<https://data.example.gouv.fr/org/anssi>
    a legal:LegalEntity, org:Organization ;
    legal:legalName      "Agence nationale de la sécurité des systèmes d'information"@fr ;
    legal:alternativeName "ANSSI"@fr ;
    legal:legalIdentifier "13000303500017" ;
    schema:url           <https://www.ssi.gouv.fr> .

<https://data.example.gouv.fr/org/cada>
    a legal:LegalEntity, org:Organization ;
    legal:legalName      "Commission d'accès aux documents administratifs"@fr ;
    legal:alternativeName "CADA"@fr .
```

---

## 5. Charger les données

=== "Via curl"

    ```bash
    curl -X POST http://localhost:7878/store \
      -H "Content-Type: text/turtle" \
      --data-binary @organisations.ttl
    ```

=== "Via l'interface web"

    1. Ouvrez **http://localhost:7878**
    2. Cliquez sur **"Upload"**
    3. Sélectionnez votre fichier `.ttl`
    4. Cliquez **"Upload"**

=== "Avec un graphe nommé"

    ```bash
    # Charger dans un graphe nommé (recommandé en production)
    curl -X POST \
      "http://localhost:7878/store?graph=https://data.example.gouv.fr/graph/organisations" \
      -H "Content-Type: text/turtle" \
      --data-binary @organisations.ttl
    ```

!!! tip "Bonne pratique"
    Chargez toujours vos données dans un **graphe nommé**. Cela permet de gérer plusieurs jeux de données indépendants et de les mettre à jour sans tout réimporter.

---

## 6. Interroger avec SPARQL

### Via l'interface web

Ouvrez **http://localhost:7878** et collez cette requête :

```sparql
PREFIX legal: <http://www.w3.org/ns/legal#>

SELECT ?org ?nom ?siret
WHERE {
  ?org a legal:LegalEntity ;
       legal:legalName ?nom ;
       legal:legalIdentifier ?siret .
}
ORDER BY ?nom
```

### Via curl

```bash
curl -G http://localhost:7878/query \
  --data-urlencode "query=PREFIX legal: <http://www.w3.org/ns/legal#>
SELECT ?nom WHERE {
  ?org a legal:LegalEntity ;
       legal:legalName ?nom .
}" \
  -H "Accept: application/sparql-results+json"
```

### Via Python

```python
import requests

ENDPOINT = "http://localhost:7878/query"

query = """
PREFIX legal: <http://www.w3.org/ns/legal#>
SELECT ?nom ?siret WHERE {
  ?org a legal:LegalEntity ;
       legal:legalName ?nom .
  OPTIONAL { ?org legal:legalIdentifier ?siret . }
}
ORDER BY ?nom
"""

r = requests.get(
    ENDPOINT,
    params={"query": query},
    headers={"Accept": "application/sparql-results+json"}
)

for row in r.json()["results"]["bindings"]:
    print(row["nom"]["value"], row.get("siret", {}).get("value", "—"))
```

---

## 7. Mettre à jour les données (SPARQL Update)

```bash
curl -X POST http://localhost:7878/update \
  -H "Content-Type: application/sparql-update" \
  --data "
PREFIX legal: <http://www.w3.org/ns/legal#>
PREFIX schema: <http://schema.org/> 

INSERT DATA {
  <https://data.example.gouv.fr/org/cnil>
    a legal:LegalEntity ;
    legal:legalName      \"Commission nationale de l'informatique et des libertés\"@fr ;
    legal:alternativeName \"CNIL\"@fr ;
    schema:url           <https://www.cnil.fr> .
}
"
```

---

## 8. Vérifier le contenu chargé

```sparql
# Combien de triplets ?
SELECT (COUNT(*) AS ?total) WHERE { ?s ?p ?o . }

# Quels graphes nommés ?
SELECT DISTINCT ?g WHERE { GRAPH ?g { ?s ?p ?o . } }
```

---

## Vérifiez votre compréhension

- [ ] Quelle est la différence entre `/store` et `/query` dans l'API Oxigraph ?
- [ ] Pourquoi charger les données dans un graphe nommé plutôt que dans le graphe par défaut ?
- [ ] Comment supprimer tous les triplets d'un graphe nommé sans supprimer les autres ?

---

## Étape suivante

→ [Guide — Déployer Oxigraph comme endpoint SPARQL](../guides/04-oxigraph-deploiement.md)  
→ [Référence — API et configuration Oxigraph](../references/sparql-oxigraph.md)