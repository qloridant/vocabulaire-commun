# Guide — Déployer Oxigraph comme endpoint SPARQL

> **Problème** : Vous souhaitez exposer vos données RDF via un endpoint SPARQL public ou interne, stable et sécurisé.  
> **Solution** : Déployer Oxigraph derrière un reverse proxy (nginx) avec authentification.

---

## Quand utiliser ce guide

- Vous avez validé vos données en local (voir [Tutoriel 05](../tutoriels/05-oxigraph-local.md))
- Vous souhaitez exposer un endpoint SPARQL à d'autres services ou équipes
- Vous êtes sur un serveur Linux (VPS, VM, conteneur)

---

## 1. Architecture cible

```
Internet / réseau interne
        │
        ▼
   nginx (443/HTTPS)          ← reverse proxy, TLS, auth basique
        │
        ▼
  Oxigraph (7878)             ← triplestore, écoute en local uniquement
        │
        ▼
  /var/lib/oxigraph/          ← stockage persistant
```

---

## 2. Déploiement avec Docker Compose

Créez un fichier `docker-compose.yml` :

```yaml
services:
  oxigraph:
    image: ghcr.io/oxigraph/oxigraph:latest
    restart: unless-stopped
    volumes:
      - oxigraph-data:/data
    command: serve --location /data --bind 0.0.0.0:7878
    # Pas exposé directement — nginx fait le proxy
    expose:
      - "7878"

  nginx:
    image: nginx:alpine
    restart: unless-stopped
    ports:
      - "443:443"
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
      - ./certs:/etc/nginx/certs:ro
      - ./.htpasswd:/etc/nginx/.htpasswd:ro

volumes:
  oxigraph-data:
```

---

## 3. Configuration nginx

Fichier `nginx.conf` :

```nginx
server {
    listen 80;
    server_name sparql.example.gouv.fr;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name sparql.example.gouv.fr;

    ssl_certificate     /etc/nginx/certs/fullchain.pem;
    ssl_certificate_key /etc/nginx/certs/privkey.pem;

    # Endpoint SPARQL public (lecture seule)
    location /query {
        proxy_pass http://oxigraph:7878/query;
        proxy_set_header Host $host;

        # CORS pour les clients web
        add_header Access-Control-Allow-Origin "*";
        add_header Access-Control-Allow-Methods "GET, POST, OPTIONS";
    }

    # Interface web et écriture — accès restreint
    location / {
        auth_basic "Accès restreint";
        auth_basic_user_file /etc/nginx/.htpasswd;

        proxy_pass http://oxigraph:7878;
        proxy_set_header Host $host;
    }
}
```

---

## 4. Créer les identifiants d'accès

```bash
# Installer htpasswd (si absent)
apt install apache2-utils

# Créer le fichier avec un premier utilisateur
htpasswd -c .htpasswd agent_technique

# Ajouter un utilisateur supplémentaire
htpasswd .htpasswd autre_agent
```

---

## 5. Lancer le service

```bash
docker compose up -d

# Vérifier les logs
docker compose logs -f oxigraph
```

---

## 6. Charger les données initiales

```bash
# Depuis votre poste local, en passant par nginx avec authentification
curl -u agent_technique:motdepasse \
  -X POST "https://sparql.example.gouv.fr/store?graph=https://data.example.gouv.fr/graph/organisations" \
  -H "Content-Type: text/turtle" \
  --data-binary @organisations.ttl
```

---

## 7. Automatiser le rechargement des données

Script `reload-graph.sh` :

```bash
#!/bin/bash
# Recharge un graphe nommé depuis un fichier Turtle
# Usage : ./reload-graph.sh organisations.ttl https://data.example.gouv.fr/graph/organisations

FILE=$1
GRAPH=$2
ENDPOINT="https://sparql.example.gouv.fr"
AUTH="agent_technique:motdepasse"

# Supprimer l'ancien graphe
curl -u "$AUTH" -X DELETE \
  "${ENDPOINT}/store?graph=${GRAPH}"

# Charger le nouveau fichier
curl -u "$AUTH" -X POST \
  "${ENDPOINT}/store?graph=${GRAPH}" \
  -H "Content-Type: text/turtle" \
  --data-binary "@${FILE}"

echo "Graphe ${GRAPH} rechargé depuis ${FILE}"
```

---

## 8. Monitorer le service

```bash
# Nombre de triplets chargés
curl -G "https://sparql.example.gouv.fr/query" \
  -u "agent_technique:motdepasse" \
  --data-urlencode "query=SELECT (COUNT(*) AS ?n) WHERE { ?s ?p ?o }" \
  -H "Accept: application/sparql-results+json"

# Liste des graphes
curl -G "https://sparql.example.gouv.fr/query" \
  -u "agent_technique:motdepasse" \
  --data-urlencode "query=SELECT DISTINCT ?g WHERE { GRAPH ?g { ?s ?p ?o } }" \
  -H "Accept: application/sparql-results+json"
```

---

## Limites d'Oxigraph à connaître

| Capacité | Valeur indicative |
|---|---|
| Triplets recommandés | < 100 millions |
| Requêtes fédérées (`SERVICE`) | Supporté |
| Raisonnement OWL | Non supporté nativement |
| Transactions ACID | Oui |
| Réplication / clustering | Non (instance unique) |

!!! warning "Pour de grands volumes"
    Au-delà de 100 millions de triplets, orientez-vous vers **Apache Jena / Fuseki** ou **Virtuoso Open Source**.

---

## Étape suivante

→ [Référence — API et configuration Oxigraph](../references/sparql-oxigraph.md)  
→ [Guide — Requêtes SPARQL](./02-sparql-requetes.md)