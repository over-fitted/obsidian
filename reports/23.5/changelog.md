- Added the following to offline neo4j notes:
	- [[neo4j notes#unwind]]
	- [[neo4j notes#yield]]
	- [[neo4j notes#Nested Values]]
- Attempted to manually insert json object using custom cypher query
- Found that $ cannot be used in json input
	- $ is used to denote a neo4j parameter
	- could handle it in java middleware
- Attempted to run neo4j_doc_manager
	- read up on mongo replica_set
	- Ran into too many minor issues to get working today
- - Got movies graph to work with 70000+ nodes
- finalised movies and comments with trimmed properties as being a valid dataset
	- other collections except users are not referenced by each other, and users reference is not useful.
	- shortlisted collections should suffice in showing basic 2-domain cross-domain queries
- Added findings about neo4j syntax to offline docs
- set up java workspace
	- for whatever reason gradle refused to allow `compile 'org.neo4j.driver:neo4j-java-driver:4.4.5'` as per neo4j for java docs
	- `implementation 'org.neo4j:neo4j:4.4.7'` 
	- not done, can't figure out why my gradle stuff does not work, to continue tomorrow
		- may just use Ryan's implementation as a base