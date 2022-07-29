polyglot persistence with mongodb and neo4j
- polyglot persistence = using different dbms for different purposes
- factors:
	- data migration between graphdb and mongo
		- migrate all, subset or duplicate
- mongo+neo usecases
	- browse and search e.g. catalog -> mongo
	- recommendations -> neo4j
- maintaining consistency
- [[json ingest]] 
- Can use APOC to retrieve data as JSON from mongodb, then unwind into a map data structure and use FOREACH to handle the data programmatically
	- can customise the cypher query
-

