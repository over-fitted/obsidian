# Data modelling
Spring Data can be used with Neo4j. This facilitates the mapping of Neo4j entities as Java objects, with properties and relationships automatically hydrated, while providing an easier learning curve for those already familiar with Spring data.

## Entities in spring
- Nodes and edges are represented as `Node` and `RelationshipProperties `entities, hydrated when returned as query results from corresponding Spring repositories
    - Nodes are first class entities but *edges are not*. Implications of this will be seen in later sections.
- neo4jrespositories are available which must be assigned to a Node class and its corresponding primary key's type.
    - Note that edges cannot have a neo4jrepository since they are not first class entities
- Inheritance is supported but documentation on this is very sparse. 
    - All labels represented by parent classes will be saved with the node, in addition to the actual class'.
    - This can be utilised through polymorphism in the query section.
- Labels can be derived from entity class using instanceOf. 
- Labels not caught this way and ONLY these labels can be derived using Dynamic labels. Exhaustive list of all labels can be derived using Labels() function in neo4j but not trivial in spring.

## Relationships(edges)
- Relationships are stored as lists of nodes/relationshipentities in a user-determined "root" node, with a specified direction
- relationshipentities store sink nodes but *not* source nodes to prevent cyclical reference. They can also store additional information such as body information as per node entities.
- Relationships cannot be stored in non-root nodes as this would result in a cyclical reference 
    - Root node is a significant design decision as retrieving relationships from non-root nodes is non-trivial
- More than one edge of the same type with same source and sink can be stored
    - Trivial to store edges with different contents when using relationshipproperties as class in edgelist of domain entity
    - When edgelist stores edges as a list of destination nodes, duplicate edges can still be represented with the same node appearing in the list multiple times

## Schema
#### Neo4j
- Neo4j itself requires no fixed schema
    - fields that are not present in a node are represented as "null" in queries
        - e.g.: `MATCH(n:Movie) WHERE n.title IS NULL`
    - Typing is inferred from inputs but can be dynamically changed
#### Spring Data for Neo4j
- Spring data for Neo4j necessarily enforces schema due to data domain entity system
- Extra properties stored in neo4j nodes that are not defined in domain class are ignored by Spring Data
    - such properties are persisted through updates
- Properties in neo4j nodes with the same name as a property in neo4j but different data type will result in Uncoercible error
- Properties defined in domain class not present in neo4j nodes are reflected as null


## Primary Key
#### Neo4j
- PKs are forcefully auto-generated and not settable by users
    - PKs are only internally used for indexing-related purposes
    - due to immutability and auto-generation, PKs in neo4j will never encounter duplication issues
- PK can be derived using `ID(node)` function
- Uniqueness constraint can be set
- Existence and key constraints are only settable by enterprise users
#### Spring Data for Neo4j
- Cannot access Neo4j internal IDs through domain entity objects, need to use `ID()` function.
    - Theoretically could return a projection with neo4j ID as a string, but this has not been tested
- Nodes require PKs which can be user-generated as with business keys or auto-generated with UUIDGenerator
- RelationshipProperties require a Long id denoted with `@RelationshipId` annotation
- Objects with duplicate keys cannot be created via Spring as using repository.save would cause an update replacing the old object with the new object
- Primary key of Spring data is independent of Neo4j's. As such, objects with duplicate values under spring key fields can be created when interacting directly with neo4j as with using neo4j desktop, without any warnings provided
    - i.e. new movie nodes can be created with same javaid as a pre-existing movie node
- Duplicate keys will only cause an error to be thrown when user attempts to save or update an object with duplicate key 
    - nodes with key field set as null in neo4j can be safely returned
- If PK is null
    - no error is thrown for returning results of a search query
    - error thrown if attempting to save the object, unless an ID-generator is defined 

## Nested Data Handling
Can be handled in 4 main ways
- map-flattening - i.e. change tomatoes.score to tomatoes_score, store each map key-value pair as a property
    - preferred when nested property is frequently used
- serialisation - i.e. JSON serialise map into a string, deserialise using APOC function
    - preferred when nested properties' structure unknown and map is infrequently used, may be used as an archive
    - harder to perform query w
- Java map - POJO can have non-nested map as a property i.e. no map in a map
    - Properties would be "flattened" as tomatoes.score in neo4j, but accessible from tomatoes map in Java
- separate node - create a new node containing the map's properties and connect to the parent node via an edge
    - particularly useful when different nodes can share such edges, which should be a frequent occurence when dealing with data taken from a document store



# Queries
### Neo4j Desktop
- Jupyter notebook-like interface is provided
- Returned data can be represented as table of JSON strings, ASCII table or interactive graph
    - nodes in graph can be expanded to view adjacent edges and nodes
- Cypher language must be used. APOC procedures can be enabled without additional imports to provide greater functionality such as data consumption or more robust filters

### Spring Data with Neo4j
- Queries can be defined either by repository method name,  custom compile-time static queries or cypherDSL
- neo4jrepositories can only return their corresponding class or primitives.
    - Can bypass class limitation with the use of polymorphism
        - Define a generalised `node` class that can take in any node
        - have all other nodes extend from it
            - inheritance in spring data is sparsely documented and vague
    - need to use service class to retrieve edges from root nodes, rather than just querying for the edges directly
- Can use optional, list, iterable, nodeEntityType, pageable as return type
    - should look into pageable 
    
#### Repository method name
- Provides default search methods with syntax `findByProperty`
	- full functionality is not well documented in spring data for neo4j, might be able to find more information from spring data documentation for other data modules or JPA
	- Recommended to utilise parameters using @Param in arguments
		- can cache execution plans
- When using queries defined by repository method, all relationships will be hydrated 1-layer deep
    - i.e. `findMovieByTitleRegex` method name returns movies with all directors and other edges hydrated
    - `findUserById` in using a custom query does not hydrate any edges, regardless of if path or node is returned.
- Can sort with Asc or Desc appended to end of method name
- Can filter for firstX
- Provides regex functionality by appending Regex to the end
	- invalid regex throws exception `org.neo4j.driver.exceptions.ClientException: Invalid Regex: Dangling meta character '*' near index 0 *.itanic`
#### Static compile-time query
- Cypher query specified using @Query as per JPA
- Cannot modify at run-time other than with Cypher parameters
    - Cannot use parameters in query path length
    - Cannot alter pattern such as adding additional nodes
    - Bypass the weaknesses here using expandPathConfig 
- Can return primitives, path or nodes. Relationships are only hydrated in nodes returned if at least one path is returned and the target relationship is present in at least one returned path
- When returning paths in a cypher query, when the return type of the repository method is a list of nodes, all nodes present in the paths corresponding to the return type of the method are returned only once in the list, with all edges present in all returned paths hydrated
    - Where possible the list tries to preserve the order in which the nodes are traversed in paths. This is sufficient when only 1 path is returned. However, when dealing with multiple paths, paths are not delineated and since nodes are not repeated, previously seen nodes may be missing from certain path "traversals"
    - If multiplepaths are required, it would be better to return a list of strings where each string represents a path using node IDs to represent each node. The user can then manually query for these nodes where necessary
- Can use =~ in WHERE clause for regex
	- Case-sensitive

#### CypherDSL
General idea 
- generate cypher queries either using raw strings, or using a type-safe query-builder
- Queries can then be passed into a repository extending CypherdslConditionExecutor and/or CypherdslStatementExecutor
- Repository can then return one or all result entities

Issues
- Starter guide documentation for use with spring data is very lacking, mostly consisting of a cookbook with narrow use-case. Only queries returning nodes are discussed, excluding paths, primitives and DTOs
- Repositories appear to be unable to return primitives such as strings, posing a problem for returning paths in the method prescribed above
- Javadocs are present but hard to follow
    - generally refer to Cypher static class as entry point from which to generate most of the elements required in using the DSL such as path or node
- Research into cypherDSL was cut short in favour of pursuing list-comprehension methods with static queries to overcome inability to change queries based on number of arguments at runtime.

## Dynamic Properties
- Global accumulators in Tigergraph have similar functionality to WITH variables in Cypher queries, which can take in values from list methods like all() or reduce()
- Local accumulators could theoretically be set and deleted by the user for precalculation but are not natively supported.
    - we can use pre-calculation queries to add edges or properties with a reserved label/key and use these temporary edges or properties in our main query. We can then remove all such edges and properties.

# Evaluation involving endpoints and code, Non-User Stories V2
## Create
Nodes are created by instantiating the node instance as an object, then calling repository.save() with the node instance as the argument.

Objects can be instantiated from rest call as seen in the example below

#### `/movies/new`
![[Pasted image 20220715185603.png]]
```java
@PostMapping("/new")  
public ResponseEntity createMovie (@RequestBody Movie movie){  
    basicCreateService.createMovie(movie);  
    return ResponseEntity.ok(HttpStatus.OK);  
}
```
```java
public void createMovie (Movie movie){  
    movieRepository.save(movie);  
}
```
`/movies/new`  creates new movie nodes using data in request body, via repository default save method as seen in code above. No additional modification to movieRepository is required as save is already a method.

Issue with CREATE is that nodes cannot be created with additional fields not defined in Java entity class while using Java Spring Boot.

## k-hop traversal
Can be trivially done using static query between 2 points with the following pattern:
`(source:nodeType)-[edgeAllowed*k..d]-(sink:nodeType)`
Will be seen in the user stories endpoints so example omitted

## Hard Requirements - Regex
Fields of nodes and edges can be queried by regex in both static query and with default repository method. Cypher uses `=~` as regex equality operator in place of `=`, while the default repository just needs `Regex` appended to end of the method.

![[Pasted image 20220718113153.png]]
This query utilises the default repository method as seen here 
`List<Movie> findMovieByTitleRegex(String title);`

## Shortest Path
![[Pasted image 20220718113435.png]]
Shortest path can be found using Neo4j built-in shortest path function. Cypher uses BFS by default so this query stops at the first path it sees, so it is optimised without needing to specify an upperbound on range assuming a suitable path exists
```java
@Query("MATCH p=shortestPath((source:Movie {title: $movie1})-[:Casts*]-(sink:Movie {title: $movie2}))\n"  
        + "RETURN p"  
)  
List<Movie> findShortestPathBetween(@Param("movie1") String movie1, @Param("movie2") String movie2);
```

# User Stories V2
### Hard Requirements - Cross Domain Queries
![[Pasted image 20220718144049.png]]
- As a user, I want to view all the **links** **among casts**, so that I know how casts know each other.
```java
@Query("MATCH (m:Cast)\n" +  
        "MATCH (n:Cast)\n" +  
        "CALL apoc.path.expandConfig(m, {\n" +  
        "    minLevel: $minRange,\n" +  
        "    maxLevel: $maxRange,\n" +  
        "    terminatorNodes:[n],\n" +  
        "    limit: $limit,\n" +  
        "    relationshipFilter: 'Casts'\n" +  
        "})\n" +  
        "YIELD path as p\n" +  
        "return reduce(b=nodes(p)[0].id + ', ',i in range(1,length(p)) | b + TYPE(relationships(p)[i-1]) \n" +  
        "+ CASE startnode(relationships(p)[i-1]) WHEN nodes(p)[i-1] THEN '>' ELSE '<' END \n"  
        + "+ ', ' + nodes(p)[i].id + ', ')")  
List<String> allCastPaths(@Param("minRange") int minRange, @Param("maxRange") int maxRange,  
                          @Param("limit") int limit);
```
- apoc.path.expandConfig is required to set minrange and maxrange using parameters, unlike ascii patterns which can only use a path length range fixed at compile-time as with the following pattern `(m:Cast)-[:Casts*1..3]-(n:Cast)`
- reduce statement is used to separate paths into different strings containing the ID of each node in the path and each edge between pairs of nodes. This overcomes the problem of having all paths melded together into a pile of hydrated nodes, which would occur if we directly returned the path as `List<PathNode>` as with TigerGraph.
- Query is very memory and time-intensive due to going from every cast member to every other cast member. Valid range is also very short because path can traverse from cast to movie to genre to a massive number of movies.

### Hard Requirements - Cross Domain Queries (Same node type)
As a user, I want to view all the **links between cast A, cast B, cast C**,… to find out how they know each other
![[Pasted image 20220718145531.png]]
- Uses same query as previous story, but with a condition that
    1. the source and sink nodes must be within the list of cast nodes given 
    2. all in the list must be in the returned path
- first condition is defined using WHERE statements in the first two lines
- second condition is defined using the ALL statement before the return statement
- since only the names specified must correspond to Cast nodes, list comprehension with `WHERE 'Cast' in labels(n)` is used to filter the list of possible nodes that can fulfill the name requirement
```java
@Query("MATCH (m:Cast) WHERE m.name in $castNames\n" +  
        "MATCH (n:Cast) WHERE n.name in $castNames\n" +  
        "CALL apoc.path.expandConfig(m, {\n" +  
        "    minLevel: $minRange,   \n" +  
        "    maxLevel: $maxRange,\n" +  
        "    terminatorNodes:[n],\n" +  
        "    limit: $limit\n" +  
        "})\n" +  
        "YIELD path as p\n" +  
        "WHERE ALL(target in $castNames WHERE target in [n in nodes(p) WHERE 'Cast' in labels(n) | n.name])\n" +  
        "return reduce(b=nodes(p)[0].id + ', ',i in range(1,length(p)) | b + TYPE(relationships(p)[i-1]) \n" +  
        "+ CASE startnode(relationships(p)[i-1]) WHEN nodes(p)[i-1] THEN '>' ELSE '<' END \n"  
        + "+ ', ' + nodes(p)[i].id + ', ')")  
List<String> castTraversal(@Param("minRange") int minRange,  
                           @Param("maxRange") int maxRange,  
                           @Param("limit") int limit,  
                           @Param("castNames") List<String> castNames);
```
### Hard Requirements - Cross Domain Queries (Mixed node type)
As a user, I want to view all the **links between cast A, movie A**… to find out how cast A is linked to movie A
![[Pasted image 20220718181414.png]]
-   Since property should work on both selected movie and cast nodes, ID was selected
-   Similar query to previous story but without list comprehension with WHERE 'Cast' in labels(n)