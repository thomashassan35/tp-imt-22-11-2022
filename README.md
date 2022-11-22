# tp-imt-22-11-2022
Gestion des méga-données. Introduction NoSQL, graphes et sémantique

# Gestion des méga-données. Introduction NoSQL, graphes et sémantique





## Partie 1 : graphes sémantiques / RDF / knowledge graphs

L'ojectif de cette section est d'introduire les formats de données RDF et le langage de requête SPARQL à travers des exemples que vous pouvez exécuter sur un graphe de connaissance que vous construirez, puis sur des bases de données (SPARQL endpoints) publiques. Il existe de nombreux endpoints sparql publics qui servent des graphes de connaissances variés, et potentiellement liés (Linked Data). 
Une liste non exhaustive est disponible [ici](https://www.wikidata.org/wiki/Wikidata:Lists/SPARQL_endpoints). 


### construction et requêtage d'un graphe de connaissances de la classe 

Pour cet exercice nous allons utiliser l'ontologie Friend Of A Friend (foaf) : `http://xmlns.com/foaf/0.1/` 
L'objectif va être de construire collectivement un graphe de connaissances des individus de la classe, puis de le visualiser/requêter. 

Parmi les informations que nous allons pourrions au graphe, nous allons nous focaliser sur les faits suivants : 
La déclaration des individus, i.e. les noeuds de notre graphe. Au sens RDF, ce sont des instances (ou assertions, ABox). Nous utiliserons la classe foaf:Person pour les représenter. Cette déclaration se fait de la forme suivante (syntaxe `turtle`):

```
<http://www.example.com/tp-imt/TH> rdf:type foaf:Person
```
Alternativement, pour déclarer la classe d'uns instance, la forme raccourcie suivante est valide ('a' remplace 'rdf:type'): 

```
<http://www.example.com/tp-imt/TH> a foaf:Person
```

Notez que nous utilisons des `préfixes` RDF. Ces préfixes doivent être déclarés en entête d'un payload / d'une requete pour être reconnus : 

```
@prefix om-owl:  <http://knoesis.wright.edu/ssw/ont/sensor-observation.owl#> .
@prefix rdfs:    <http://www.w3.org/2000/01/rdf-schema#> .
@prefix sens-obs:  <http://knoesis.wright.edu/ssw/> .
@prefix example:  <http://www.example.com/test/> .
@prefix owl:     <http://www.w3.org/2002/07/owl#> .
@prefix xsd:     <http://www.w3.org/2001/XMLSchema#> .
@prefix weather:  <http://sonicbanana.cs.wright.edu/ssw/ont/weather.owl#> .
@prefix rdf:     <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix wgs84:   <http://www.w3.org/2003/01/geo/wgs84_pos#> .
@prefix prefix-a:   <http://orange-labs.fr/fog/ont/iot.owl#> .
@prefix geo2007:   <http://www.opengis.net/gml/> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .


<http://www.example.com/tp-imt/TH> rdf:type foaf:Person
```

La seconde information que nous allons ajouter est simplement le lien de connaissance entre tous les élèves, et l'intervenant.
Vous pouvez également ajouter des personnes supplémentaires au graphe (professeurs, connaissances...), voire votre animal de compagnie (utilisez la classe <http://dbpedia.org/ontology/Animal>). 
Etant donné qu'il s'agit d'une relation, nous utiliserons une ObjectProperty : `http://xmlns.com/foaf/0.1/` 

Pour donner un nom à vos instances, vous utiliserez les propriétés RDF (DataProperties) suivantes: 
`http://xmlns.com/foaf/0.1/givenName`
`http://xmlns.com/foaf/0.1/familyName`

### requêtage d'enpoints sparql publics 


#### Requêtes sémantiques sur le graphe de connaissance Prix Nobel 

En cliquant sur l'option requête avancée (advanced), la requête par défaut suivante est affichée :  

```
PREFIX nobel: <http://data.nobelprize.org/terms/>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

SELECT DISTINCT ?name ?prize WHERE {
	?person rdf:type nobel:Laureate;
		rdfs:label ?name;
		nobel:nobelPrize ?prize .
	?prize rdfs:label ?prizeName ;
        ?prize 
  FILTER (lang(?prizeName) = 'en')
} LIMIT 10
``` 

Vous noterez que cette requête contient différents préfixes, pour l'ontologie foaf mais également pour l'ontologie spécifique aux prix nobels, et aux voacabulaires rdf et rdfs, évoqués dans la partie cours. Ces vocabulaires, avec leur extension OWL, sont le squelette de toutes les autres ontologies. 

Avant d'exécuter cette requête, expliquez là en langage naturel ainsi que le résultat attendu.

A l'aide de la [documentation](https://www.w3.org/TR/sparql11-query/#select), essayez désormais de construire les requêtes correspondant aux questions suivantes : 

Trouver le(s) dernier prix Nobel de nationalité française. 
Trouver la date, le nom et la nationalité du 1er et du dernier Nobel décerné toutes catégories confondues.

Vous aurez notamment besoin d'utiliser les DataProperties foaf de la section précédente.

#### Requêtes sémantiques sur le graphe de connaissance DBPedia  

Le graphe de connaissance DBPedia est un graphe correspondant aux faits déclarés dans wikipedia, extraits et structurés de façon automatisée (voir https://fr.wikipedia.org/wiki/DBpedia). 
Exemple de requêtes (je m'excuse pour les non-afficionado de football, dont je fais partie) : 

```
SELECT *
WHERE
     {
        ?athlete rdfs:label "Cristiano Ronaldo"@en
     }
```

```
SELECT
     ?athlete
     ?number
     ?cityNameWHERE
{
  ?athlete  rdfs:label      "Cristiano Ronaldo"@en ;
            dbo:number      ?number ;
            dbo:birthPlace  ?place .  ?place    a               dbo:City ;
            rdfs:label      ?cityName ;
            dbo:region      ?region .  FILTER ( LANG ( ?cityName ) = 'en' )
}
```

Utilisez son [endpoint public](https://dbpedia.org/sparql) pour répondre aux questions suivantes, à l'aide des exemples ci-dessus et de la documentation : 

Quel est le nombre total de buts marqués par Zinedine Zidane ? 
Vous pourrez utiliser la propriété `https://dbpedia.org/property/totalgoals` 

Retrouvez tous les individus dobnt le nombre total de buts excède celui de Zinedine Zidane.  

Retrouvez la taille, le poids, et toutes les médailles d'or de Teddy Rinner. Vous pouvez vous aider directement de sa page de profil dbpedia pour trouver les relations et propriétés dont vous aurez besoin pour construire votre requête : https://dbpedia.org/page/Teddy_Riner
 

## Partie 2 : introduction BDD NoSQL, graphes, multi-modèle

### Mise en oeuvre d'une BDD multi-modèle ArangoDB

Dans cette section vous allez mettre en oeuvre et configurer une base de données ArangoDB via un driver. 
Avant toute chpse, pourquoi utiliser un driver plutôt que configurer la base à la main (via l'UI par exemple) ? 
- Dans un contexte réel de production, il est nécessaire que l'environnement de déploiement soit identique sur les machines locales de tous les contributeurs, le(s) serveurs de test, et le(s) serveurs de production. De cette façon on peut facilement développer une application au dessus de la base de donnée et s'assurer que le comportement de l'application soit identique partout. 
- De la même façon, la configuration des informations (sensibles) d'accès à la base de données se fera via un fichier de configuration, ou des variables d'environnement. 
- La configuration de la base peut évoluer, et est dépendante de l'environnement de déploiement (par exemple une instance de test configurée différemment de l'instance de production pour des raisons de dimensionnement, de sécurité, ...)
- L'accès à l'UI n'est pas possible / pas garanti (e.g. contraintes réseau). 


#### Déclaration d'une database, des collections, insertion de documents et requêtes 

En java : 
Vous pouvez vous inspirer de la [documentation complète](https://www.arangodb.com/docs/stable/drivers/java.html) et notamment de ce [tutoriel](https://www.arangodb.com/docs/stable/drivers/java-tutorial.html) pour la création de la base de données et les opérations de base (création de document, requêtes).  

En python : 
Vous pouvez vous inspirer de ce [tutoriel](https://www.arangodb.com/tutorials/tutorial-python/) pour la création de la base de données les opérations de base.


A l'aide de la documentation, créez au moins une collection de `noeuds`, et une collection d'`edges`. 

#### Optionnel : Déclaration d'index 

Avant d'insérer des données, nous devrions également configurer des index pour améliorer la performance de nos futures requêtes. 
Pour rappel, [la liste des index pris en charge par ArangoDB:](https://www.arangodb.com/docs/stable/indexing-index-basics.html)
Cette étape est notamment requise pour exécuter des requêtes géographiques.
Par défaut comme abordé en cours, un index "primaire" est créé pour les noeuds via leur propriété _key. 
Un index spécifique à la modélisation graphe est également créé pour les collections d'edges, via leur propriété _from et _to.  

#### Optionnel : Importation de données via l'interface utilisateur et requêtes ArangoDB

Nous allons importer un jeu de données déjà constitué, situé dans ce dépôt (fichiers edges.json et nodes.json).
Ce jeu de données est relatif à un use case smart city, et correspond à un graphe sémantique d'une ville avec des données géolocalisées. Le graphe va nous permettre de mettre en application différentes formes de requêtes et ainsi exploiter la versatilité de la base multi-modèle (document/JSON, graphe, géographique, texte...). 

Vous pouvez exécuter vos requêtes via votre code, ou l'UI.
Pour accédez à la BDD via l'interface web exposée sur le port que vous avez spécifié (8529 par défaut) : 127.0.0.1:8529

Votre login/mot de passe vous sera demandé (si vous avez lancé votre propre instance, faites en fonction de ce que vous avez indiqué dans l'étape de démarrage de l'arangodb). 

Une fois connecté, nous sommes redirigié vers la base de données "système" par défaut. Vous devriez pouvoir sélectionner la base de données créée à l'étape précédente. La base de données peut également être créée par l'interface web (panneau de gauche -> databases -> +). Une fois la base de données créée, on s'y connecte à tout moment en cliquant sur l'onglet DB en haut à droite de l'écran.

Si vous nous reste à créer des collections, une pour nos futurs noeuds du graphe, que l'on nommera nodes, de type "Document", et une pour les arcs du graphe que l'on nommera edges, de type "Edge" (respecter ce nommage).

Importation des données :
	télécharger les fichiers nodes.json et edges.json présents sur ce dépôt
	Cliquer sur la collection nodes, nous allons importer le fichier nodes.json. Cliquer en haut à droite de l'écran, sur "Upload documents from JSON file"
	Répéter l'opération depuis la collection edges, avec le fichier edges.json
	Vous devriez avoir environ 6k documents chargés dans nodes, et 76 documents dans edges

Nous allons effectuer quelques requêtes sur le jeu de données ainsi importé, et vous trouverez comment exprimer certaines requêtes en langage naturel par vous même. Arangodb étant une base multi-modèle, nous allons rapidement illustrer ces différentes composantes

Requêtes Document et Graphe simples :

Récupérer tous les noeuds : `for n in nodes return n`.
Récupérer les différentes classes du jeu de données (champ virtual_class) : `for n in nodes return n.virtual_class`
Adaptez la requête des classes virtuelles en utilisant le mot clé distinct pour supprimer les duplicats
Construire un document contenant uniquement les champs iri et domain : `for n in nodes return {domain:n.domain, iri:n.iri}`
Récupérer tous les edges : `for e in edges return e`
Parcourir le graphe dans le sens entrant (`inbound`) et retourner un set composé des noeuds et des edges parcourus : 
`for n in nodes for v,e in 1..1 inbound n edges return {n,e,v}`. 
Quels sont les noeuds retournés ? Construire un payload de retour comprenant uniquement leur classe et leur position (champ "cG9z_geom_point")

Requêtes géographiques : Pour ces requêtes, un index géographique sera nécessaire. Commencez par créer un index géographique sur votre collection nodes . Le champ géographique qui sera indexé est le suivant : La documentation suivante vous sera utile : https://www.arangodb.com/docs/stable/aql/tutorial-geospatial.html.

Récupérer tous les noeuds proches du point de coordonnées latitude 5.772045969859884, longitude 45.206042961330446, dans un rayon de 100m. Utilisez la documentation suivante comme exemple https://www.arangodb.com/docs/stable/aql/functions-geo.html (fonction geo_distance). Pour définir le point avec ces coordonnées, vous pouvez au choix créer un payload json, ou utiliser la fonction geo_point
Construisez un résultat comprenant les noeuds, leurs un arc sortants vers d'autres noeuds, et leur distance au parking de la mairie sans spécifier les coordonnées du parking. Le filtre suivant vous permet de retrouver ce noeud dans votre requête : FILTER node.aHR0cDovL3B1cmwub3JnL2RjL3Rlcm1zL2Rlc2NyaXB0aW9u == "Parking Mairie"




## Partie 3 : requêtes sur des property graph sémantiques de la plateforme Thing'in the future

Dans cette section nous allons utiliser la plateforme Thing'In the future, ou Thing'In. 
Dans Thing'In, les noeuds sont nommés "Avatars", ce sont les jumeaux numériques d'objets physiques. 

La base de données sur laquelle nous allons opérer contient plusieurs millions d'avatars, donc nous allons devoir restreindre nos requêtes à un "périmètre" donné. Dans Thing'In ce périmètre est nommé "domaine" ; c'est un bucket / espace regroupant des avatars pour une même application. 
Un domaine permet de filtrer à grosse mailles notre requête, mais peut tout de même contenir un trop grand nombre d'avatars et de relations pour être visualisé facilement. Nos requêtes vont donc permettre de raffiner encore notre filtrage. 


Nous allons exécuter des requêtes pour retrouver et filtrer des avatars avec 2 langages:
- Le langage cypher (celui de Neo4j), [documentation](https://wiki.thinginthefuture.com/en/public/CypherForThinginQuery)
- Le langage propriétaire de la plateforme ("Thing'In QL" ou "TiQL"), [documentation](https://wiki.thinginthefuture.com/public/Avatars_search)



Les requêtes seront exécutées sur les graphes suivants : 
- le graphe que nous avons créé dans la section précédente, qui sera injectée dans ThingIn
- un/des graphe du domaine de la ville intelligente / jumeau numérique d'une ville
- un/des graphe du domaine Internet des Objets 

### requête simple avec cypher 
Nous allons effectuer des requêtes sur le domaine suivant :  `http://www.example.com/insa/`.
Ce domaine utilise notamment les ontologies SSN/SOSA dédiée aux devices, dogont et eupont (environnements physiques et connectés).

Pour récupérer tous les noeuds et relations : 
```json
{
  "cypher": "MATCH ({ domain:'http://www.example.com/insa/'}) RETURN *"
}
```
Les données contenues dans ce domaine décrivent un environnement physique connecté simple composés de quelques pièces, lampes, et d'un téléphone connecté, relié à des noeuds correspondants à ses capteurs (caméra, capteur lumineux...). 

A l'aide de la documentation, retrouvez le noeud correspondant au téléphone. Sa classe vous permettra de le filtrer : `http://elite.polito.it/ontologies/eupont.owl#Phone`


Toujours en utilisatn le langage cypher et en vous appuyant sur la documentation, récupérer le téléphone et ses actionneurs (ils correspondent à l'endpoint pour activer / désactiver la torche du téléphone). 
Pour créer cette requête, utilisez les relations à partir du téléphone avec la syntaxe suivante.
```
MATCH ({iri:`B_IRI`})-[:isIn]->(r:ROOM) RETURN r
```
Le type de la relation à suivre à partir du téléphone est `https://www.w3.org/2019/wot/td#hasActionAffordance` 

### requêtes sémantiques avec TiQL

Le langage TiQL est inspiré du langage de la base NoSQL MongoDB. Il est donc plutôt orienté documents, mais ajoute des fonctionnalités de parcours de graphe et de filtres sémantiques.  
#### requête simple : 


```json
{
  "query": {
    "$classes": {
      "$any_in": [
        "http://schema.org/RecyclingCenter"
      ]
    },
    "$domain": "http://thingin.orange.com/territory/76/waste/"
  }
}
```
Vous pouvez également la retrouver dans l'onglet de sélection des requêtes d'exemple, "Centres recyclage 76".
Les données étant géolocalisées, la vue carte devrait s'ouvrir par défaut. 

Vou pouvez jeter un oeil aux propriétés de chaque noeud en cliquant dessus. 
Vous observerez notamment : 
- la classe de l'objet, utilisée dans le champ _classes, présente dans notre requête. 
- la position GPS de l'objet, qui est déclarée sous la propriété http://www.opengis.net/gml/pos. Cette position est encapsulée dans un objet [GeoJSON](https://geojson.org/)
- l'adresse postale correspondate
- etc 

#### requête graphe & sémantique 
```json
  {
    "query": [

    {
      "$alias": "room",
      "$iri": "http://thingin.orange.com/ifc/Meylan_v3/ifc-0m3cTj7$v4OQQ1cxNy_G8l",
      "<-http://elite.polito.it/ontologies/dogont.owl#isIn": ["objectsInRoom", "cupboard"]
    },
    {
      "$alias": "cupboard",
        "$classes": {
          "$all_in": [
            "http://elite.polito.it/ontologies/dogont.owl#Cupboard"
          ]
        },
        "<-http://elite.polito.it/ontologies/dogont.owl#isIn": "objectsInCupboards"
      }
    ],
    "view": {}
  }   
```  

Exécutez la requête ci-dessus, puis grâce à la visualisation du résultat en vue graphe 2D, et à la documentation, essayez d'expliquer ce que fait la requête en langage naturel. 

#### requête géographique 

```json
{
  "view": {},
  "query": {
    "http://www.opengis.net/gml/pos": {
      "$geoWithin": {
        "$geometry": {
          "coordinates": [
            [
              5.787326,
              45.213036
            ],
            [
              5.791091,
              45.213036
            ],
            [
              5.791091,
              45.210761
            ],
            [
              5.787326,
              45.210761
            ],
            [
              5.787326,
              45.213036
            ]
          ],
          "type": "Polygon"
        }
      }
    },
    "$domain": "http://thingin.orange.com/meylanCity/v3/"
  }
}
```

Cette requête retrouve les données (notamment mobilier urbain) dans un périmètre donné (GeoJSON). 