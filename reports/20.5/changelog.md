- attempted to use coursera dataset
	- Original code used an outdated API that is no longer functional
	- Attempted to adapt to new dataset but documentation was severely lacking, structure of data changed a lot, need to handle references between collections stored at different API endpoints and need to handle pagination
		- estimate 1 full day to adapt, decided not to pursue
- looked into [[json to graph transformation]]
	- attempted automatic transformation
		- read through apoc [[fromDocument]] and https://medium.com/neo4j/transform-mongodb-collections-automagically-into-graphs-9ea085d6e3ef
		- documentation is severely lacking with respect to config and how to specify mapping for nested data
			- referenced https://github.com/neo4j-contrib/neo4j-apoc-procedures/blob/4.4/core/src/main/java/apoc/graph/Graphs.java
			- referenced for config https://github.com/neo4j-contrib/neo4j-apoc-procedures/blob/071eb592a6afe26840219e3fe00e20fda56b36e1/core/src/main/java/apoc/graph/util/GraphsConfig.java#L112 
		- encountered issue where MongoDB exported json contains nesting detailing datatype of field, e.g. 
		  "$date": {
	        "$numberLong": "1431369413000"
	      }
		- workaround by importing and re-exporting from mongodb to eliminate all datatypes except numberlong, then filtering out the remaining numberlong fields
			- should be good enough to test validity of dataset + nested methods, but need to revisit this issue for mongoconnector-related usecases
			- can look into https://www.mongodb.com/docs/database-tools/bsondump/ but [Extended JSON v2.0](https://www.mongodb.com/docs/manual/reference/mongodb-extended-json/) still isnt worked around there
			- can look into https://stackoverflow.com/questions/42011508/bson-to-json-conversion-using-mongodb-driver-java-api
			- can look into https://stackoverflow.com/questions/5699323/using-json-with-mongodb, but deprecated. see: https://stackoverflow.com/questions/52455719/mognodb-json-util-is-deprecated
			- can also try storing dates as equivalent strings, does not seem to result in loss of funtionality in queries
		- [[tinkerpop gremlin]] used to be recommended for interfacing between neo4j and mongo
- attempted to follow https://medium.com/neo4j/transform-mongodb-collections-automagically-into-graphs-9ea085d6e3ef
	- docker cannot work on csit laptop, docker desktop permission denied
	- need to figure out how bson works so i can analyse dataset structure to compare with our desired datasets
	- need to figure out how to get the mappings to work