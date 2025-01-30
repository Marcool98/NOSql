```markdown
# TP1 NoSQL

Les quatre familles de systèmes de gestion de bases de données NoSQL sont :

## Bases de Données Orientées Colonnes:
Exemples : Cassandra  
Caractéristiques : Stockent les données en colonnes plutôt qu'en lignes, ce qui permet une meilleure performance pour les lectures et les écritures de grandes quantités de données.

## Bases de Données Orientées Documents:
Exemples : MongoDB, CouchDB.  
Caractéristiques : Stockent les données sous forme de documents JSON ou BSON, ce qui permet une grande flexibilité dans la structure des données.

## Bases de Données Orientées Graphes:
Exemples : Neo4j, Amazon Neptune.  
Caractéristiques : Utilisent des nœuds, des arêtes et des propriétés pour représenter et stocker les données, ce qui est idéal pour les relations complexes et les réseaux.

## Bases de Données Clé-Valeur:
Exemples : Redis  
Caractéristiques : Stockent les données sous forme de paires clé-valeur.

---

Nous allons nous focaliser sur Redis donc un système de gestion de base de données Clé-Valeur.

## Installation de Redis
Pour installer Redis, il faut faire les commandes suivantes :
```bash
sudo apt-get update
sudo apt-get install redis
```

Pour utiliser Redis, il faut tout d’abord lancer le serveur Redis avec la commande :
```bash
redis-server
```

Ensuite, on ouvre le client Redis avec la commande :
```bash
redis-cli
```

---

## Création et affichage d'un couple clé-valeur
Pour créer une clé `maCle` de valeur 10 :
```bash
SET maCle 10
```

Pour afficher la valeur de la clé `maCle` :
```bash
GET maCle
```

---

## Incrémentation/Décrémentation
Modifier la valeur d'une clé.

Dans un premier temps, on va créer une clé nommée `compteur` de valeur 0 initiale comme suit :
```bash
set compteur 0
```

Puis pour incrémenter ou décrémenter cette clé on peut utiliser respectivement les commandes :
```bash
incr compteur
decr compteur
```

---

## Clé temporaire
Mettre un timer pour faire expirer une clé

On crée dans un premier temps la clé :
```bash
set compteur 0
```

On va la faire expirer dans 120 secondes avec la commande :
```bash
expire compteur 120
```

On pourra voir le temps restant avant expiration avec la commande :
```bash
ttl compteur
```

---

## Liste de clés
On peut créer une liste de clés `mesCours` contenant le cours "Maths" avec la commande :
```bash
RPUSH mesCours "Math"
```

Pour voir le contenu de la liste on utilise la commande :
```bash
LRANGE mesCours 0 -1
```
(Tous les éléments de la liste)

Si on veut le 1er ou le 2e élément de la liste, on fait respectivement :
```bash
LRANGE mesCours 0 0
LRANGE mesCours 1 1
```

On peut pop (supprimer et afficher) la tête de la liste avec la commande :
```bash
LPOP mesCours
```

---

## Ensemble de clés
On crée un ensemble `utilisateurs` contenant "Marc" avec la commande suivante :
```bash
SADD utilisateurs "Marc"
```

Pour afficher les éléments de l'ensemble, on utilise la commande :
```bash
SMEMBERS utilisateurs
```

Pour concaténer deux ensembles `utilisateurs` et `utilisateurs2`, on utilise la commande :
```bash
SUNION utilisateurs utilisateurs2
```

---

## Ensemble ordonné
Pour ajouter un membre "Augustin" à l'ensemble "score" avec un score de 19 :
```bash
ZADD score 19 "Augustin"
ZADD score 12 "Marc"
```

Pour afficher la liste des éléments par ordre croissant de valeur :
```bash
ZRANGE score 0 -1
```
Donne :
```
"Marc"
"Augustin"
```

Pour afficher le 1er élément :
```bash
ZRANGE score 0 0
```

Pour afficher par ordre décroissant, on utilisera la commande :
```bash
ZREVRANGE score 0 -1
```
Donne :
```
"Augustin"
"Marc"
```

On peut également récupérer le rang d'un membre :
```bash
ZRANK score "Augustin"
```
Donne :
```
1 (Marc est à la position 0)
```

---

## Hash
Pour définir des champs dans un hachage nommé `tab`, on fait :
```bash
HSET tab age 22
HSET tab nom "Marc"
```

Pour récupérer tous les champs du hachage :
```bash
HGETALL tab
```
Donne :
```
"age"
"22"
"nom"
"Marc"
```

Il est possible d’incrémenter la valeur d’un champ avec :
```bash
HINCRBY tab age 4
```

L’âge du hachage sera alors de 22+4=26.

Pour récupérer un seul champ :
```bash
HGET tab age
```
Donne :
```
"26"
```

Pour afficher que les valeurs des champs, il faut faire :
```bash
HVALS tab
```
Donne :
```
"26"
"Marc"
```

---

## Pub/Sub (Publication/Souscription)
Permet aux utilisateurs de publier des messages sur des canaux et de s'abonner à ces canaux pour recevoir les messages.

Un utilisateur 1 va s'abonner à un canal `mesCours` et recevoir les messages avec la commande :
```bash
SUBSCRIBE mesCours user:1
```

Un autre utilisateur pourra publier dans ce canal avec la commande :
```bash
PUBLISH mesCours "Un cours est dispo"
```

Le 1er utilisateur va recevoir ce message dans son terminal car il est abonné à ce canal.

Pour envoyer un message à un utilisateur 1 en particulier, on fait :
```bash
PUBLISH user:1 "Bonjour user 1"
```

Pour s'abonner à un ensemble de canaux ayant le même pattern, on utilise la commande :
```bash
PSUBSCRIBE mes*
```

L'utilisateur va maintenant recevoir les messages de tous les canaux dont le nom commence par "mes".

---

## Changement de base de données
Redis donne accès à 16 bases de données par défaut (de 0 à 15), initialement, nous sommes sur la base de données 0.  
Pour changer de base de données (ici aller sur la base de données 1), il faut simplement faire :
```bash
SELECT 1
```

---

## 4 autres fonctionnalités de Redis

### Transaction
Pour faire un ensemble de commandes :
```bash
MULTI
set key1 1
set key2 2
EXEC
```

Les commandes entre `MULTI` et `EXEC` seront faites à la suite.

### Stocker des coordonnées géographiques
On utilise `GEOADD` pour ajouter des points à une structure de données géospatiale :
```bash
GEOADD lieux 2.3522 48.8566 "Paris" 4.8357 45.7640 "Lyon" 5.3698 43.2965 "Marseille"
```

Avec `GEODIST`, on peut obtenir la distance entre deux points :
```bash
GEODIST lieux Paris Lyon km
```

### Redis Streams (Gestion de flux de données)
Cela permet de stocker et traiter des flux d’événements en temps réel, utile pour les logs, la communication entre services, ou la gestion de files d’attente.  
Ajout d’un événement :
```bash
XADD mystream * temperature 25 humidity 60
```

Lecture des événements :
```bash
XRANGE mystream - +
```

### RedisJSON (Stockage de documents JSON)
Redis permet de stocker et manipuler des documents JSON directement, avec la possibilité de requêter et modifier des champs spécifiques.  
Stocker un document JSON :
```bash
JSON.SET user:1001 $ '{"name": "Alice", "age": 30, "city": "Paris"}'
```

Récupérer un champ spécifique :
```bash
JSON.GET user:1001 $.name
```

Modifier un champ JSON sans réécrire tout l’objet :
```bash
JSON.SET user:1001 $.age 31
```
```
