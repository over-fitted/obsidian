# What I have done
## Data Cleaning
1. `git clone https://github.com/neelabalan/mongodb-sample-dataset.git`
2. import each json file as a collection via mongodb compass
3. re-export each collection as a JSON file, filtering out date-type fields which are represented as numberlong in type casting
	a. 2-3 removes most of the type-casting included in the git repo's json files, which do not play well with neo4j
4. Find and replace to remove all instances of $
	a. $ is reserved in neo4j for variables
5. Create db in neo4j, click on the elipses button to the right of the db, go to open folder > import, drag and drop json file into the open file manager
6. Use apoc and cypher to import each json file, unwind and create files based on the json content as per below
## load comments as relationships via apoc
```cypher

CALL apoc.load.json("file:///comments_no_date.json") YIELD value

unwind value as q

MERGE (u:User{email:q.email})

MERGE (m:Movie{id:q.movie_id.oid})

MERGE (u)-[:Commented_On{id:q._id.oid, text:q.text}]->(m)
```
## Create PoC Java Spring Boot middleware that does basic search queries
***
# Learning Curve
Covers the developer experience at each stage of learning. Includes material that I have come across and what you can do at each stage.
# Getting Started (Optional)
Books listed provide the breadth to why graph databases and their use in network analysis are interesting
### Cypher Textbook
• https://neo4j.com/graph-databases-book/
- Available at the Library
• Comprehensive about basic Neo4j use-cases (not APOC) and skims over advanced functionality (e.g. distributed database)
### Network Science Textbooks
- http://networksciencebook.com/
- https://www.cs.cornell.edu/home/kleinber/networks-book/
• Covers network science topics such as clustering
• Very stats-intensive without that much relevance to this topic
***
# Cypher + APOC
# Overview
- Sufficient to use Neo4j Desktop and CLI Neo4j Server instances directly.
	-  Former provides a simple and quick way to analyse a set of data.
- Can further use Cypher drivers with minimal additional knowledge.
- Note: Database connectors are generally deprecated in favour of APOC, which largely involves manually replicating database contents in Neo4j. Automated methods are in stretch goals.

# Code samples
# load comments as relationships from nested document (e.g. mongodb)
```cypher

CALL apoc.load.json("file:///comments_no_date.json") YIELD value

unwind value as q

MERGE (u:User{email:q.email})

MERGE (m:Movie{id:q.movie_id.oid})

MERGE (u)-[:Commented_On{id:q._id.oid, text:q.text}]->(m)
```
# Find shortest path between 2 actors via movies (Cross-Domain)
```Cypher
MATCH p=shortestPath((bacon:Cast{name:"Kevin Miles"})-[*]-(meg:Cast{name:"Bruce Dern"}))

RETURN p
```
![[Pasted image 20220613163205.png]]
# Additional unshown features
- Regex
- Full-text search with Lucene
- Interactive graph that can show additional granular information

# Resources
## Textbook
see previous section
### Youtube
- Mostly conference videos or official talks/video series by Neo4J's official youtube channel
	- most non-official channels do not cover substantial material
	- Neo4j official videos may be paired with blog posts with more detailed transcripts and screenshots
- Mostly cover either basic Cypher use-cases as an introduction, or using Neo4J with Python for data science use-cases. Generally feel like marketing for Neo4j and do not recommend watching these videos if you already know what you want to do with Neo4j.

## Neo4j Desktop default tutorials
Intro guides provided on startup are quite substantial, wish I did them before self-studying
![[Pasted image 20220613151452.png]]

## Documentation
- No useful quick-start.
- Usually comprehensive when covering Cypher features such as unwind and yield.
	- Preferred when googling about keywords I am unsure about.
- apoc documentation is hit or miss
	- Basic use-cases and method parameters are generally provided
	- Details may be missing when doing not as obvious tasks
		- Some methods accept a config object which is never explained anywhere
	- So far have been able to eventually google for the missing material
## Cursory searches
- Usually get neo4j blog posts, documentation, stack overflow, other people's blog posts or neo4j forum posts
- neo4j blog posts and documentation are usually helpful but need to wade through a lot of bulk information
- stack overflow is pretty good
- neo4j forum posts are very hit or miss
*** 
# Neo4j Java driver
- Quickstart is very brief and not very informative
- Most of the useful material is in the `Session API` page
	- More advanced material is heavily mixed in with beginner material
	- e.g. Transactions, Sessions, causal chaining![[Pasted image 20220613163436.png]]
- Relatively easy to follow when using auto-commit transactions
	- extent of functionality needs further research
***
# Spring Data with Neo4j
# Overview
## Workflow
- Create Domain objects with Spring data, but add Neo4J-specific annotations
	- @Node, @Property, @Relationship
		- Avoid using underscore for property names as it is a reserved char
	- Can use @Id to set our own key, and use @GeneratedValue to use a generator (default to Java's)
		- Neo4j will generate internal ID regardless of whether we use it. Can be retrieved as field "Id"
- Create Repository
	- Can extend CrudRepository, Pageable or Neo4Repository
	- Generic arguments should be (Domain Class, Datatype of key)
	- Similar to Spring Data with other data modules, but have node traversal and regex
		- node traversal needs more research
- Add config details to application.properties in bootstrapped project
``` 
spring.neo4j.uri=bolt://localhost:7687  
spring.neo4j.authentication.username=neo4j  
spring.neo4j.authentication.password=123
```
## Use-case
- Relatively easy to pick up with Spring Data knowledge
	- Basic queries can be written without Cypher knowledge. Can rely on repository magic and interact with data via domain objects
		- Can further use interface-based projections - limit original fields + add derived fields
			- define interface with getters only for desired fields
			- replace domain class with defined interface in return type of method
			- ![[Pasted image 20220614152826.png]]
		- Alternatively just use class-based projection
		- can then use generics to choose whether to use projection or whole class with either case
	- can alternatively use Query By Example (supply domain object with properties set to data expected of the result)
- Custom queries can still be written via @Query and by obtaining session from neo4j driver nested inside spring data
	- @Query custom queries cannot be generated at runtime, instead may use cypher DSL or neo4j driver
# Resources
- Highly recommend first getting up to speed with Spring Boot, Spring Data and lombok
	- Spring Boot in Action
- Knowledge of Controller Service Repository may be useful
- Knowledge of Jakarta Persistence API may be useful
## Quickstarts
- Official spring data quickstart is very sparse without any queries done
- Most quickstarts and blog posts about spring data are outdated, vague or both
	- Easy to spot with `@NodeEntity` used instead of `@Node`
## Official Documentation
- Documentation largely assumes Spring Boot knowledge, and mixes in more advanced material with beginner-oriented material
- https://docs.spring.io/spring-data/neo4j/docs/current/reference/html/#preface
- Omits getters and setters in sample code
- Sample code does not build up to a usable beginner project - How to use repositories is missing
- Mixes in Reactive Programming code
	- Reactive might be worth looking into since Neo4j driver also supports this. Seems to be recommended when working with Kafka/data streams
# Issues
- Multi-level projection documentation is too vague to be particularly useful
- Reliance on java magic may reduce agility in implementing new complex queries on the fly
- Domain classes force the creation of schemas through their properties, though the properties can be set to null if not present in the database
	- need to implement null-handling
- Relationships are defined as a list of either sink domain objects or relationshipproperty objects from a root node, with no reference made in the other non-root node.
	- Might be hard to keep track of where relationships are defined
	- Java stores relationships as per how we define in the domain - when returning a list of cast members, can only see the edges if cast members are set as the root node
- Relationships are not first-class entities, can only be stored and retrieved as part of a node's properties
	- ![[Pasted image 20220615112144.png]]
	- can circumvent by not storing properties in the edges, and instead creating a node to represent the edge's properties
		- i.e. `user -> comment -> movie` instead of `user-[comment]->movie`
	- Standard way to circumvent is to return whole path and store as properties of the nodes in the path
	- Cannot represent as DTO
	- Do not think can use repository for relationship
		- does not throw error on startup but queries have issues where "cannot find root node", no available docs to guide about this
	- Cannot use multi-level interface-based projection with relationshipproperties as one of the levels ![[Pasted image 20220615131958.png]]
- Spring Data documentation has sections written to be data module-agnostic with instructions to look for more information from documentation specific to our desired module. The problem is that this was written precisely in the documentation for neo4j.
***
# Community adoption
- There is a meetup group for neo4j though it seems sparse
- Neo4j organised a conference recently.
- Neo4j seems to regularly upload to their youtube channel featuring talks on miscellaneous topics
- Many recent research papers utilising network analysis use neo4j, particularly within computational humanities
***
# TODO
## Higher priority
- cross-domain queries
- error handling with spring boot
- SLF4J
- Java-based dataset insertion
- full-text search
## Future
- context-aware network analysis
	- Mining relationship labels with NLP
	- relationship inferrence e.g. ART model
	- Clustering + finding influential hubs
## Lower priority
- Rest Spring
- Using multiple data modules e.g. mongodb with neo4j
- Traversal Framework, other Neo4j products like bloom
	- https://neo4j.com/docs/java-reference/current/traversal-framework/#traversal-java-api-path
	- Traversal framework is to be deprecated, updated version not released yet
- other connectors to neo4j i.e. JDBC, REST API 