1. `git clone https://github.com/neelabalan/mongodb-sample-dataset.git`
2. import mflix datasets to mongodb, one json file to one collection each
3. export dataset from each collection mongodb as json, filtering out date-type fields which are represented as numberlong in type casting
	a. 2-3 removes most of the type-casting included in the git repo's json files, which do not play well with neo4j
4. Find and replace to remove all instances of $
	a. $ is reserved in neo4j for variables
5. Create db in neo4j, click on the elipses button to the right of the db, go to open folder > import, drag and drop json file into the open file manager
6. Use apoc and cypher to import each json file, unwind and create files based on the json content
# load comments as relationships
```cypher

CALL apoc.load.json("file:///comments_no_date.json") YIELD value

unwind value as q

MERGE (u:User{email:q.email})

MERGE (m:Movie{id:q.movie_id.oid})

MERGE (u)-[:Commented_On{id:q._id.oid, text:q.text}]->(m)
```