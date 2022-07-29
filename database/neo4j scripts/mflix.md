# load comments as relationships
```cypher

CALL apoc.load.json("file:///comments_no_date.json") YIELD value

unwind value as q

MERGE (u:User{email:q.email})

MERGE (m:Movie{id:q.movie_id.oid})

MERGE (u)-[:Commented_On{id:q._id.oid, text:q.text}]->(m)
```
# find movies which share commentors
```cypher
MATCH p=()<-[:Commented_On]-()-[:Commented_On]->()

RETURN p LIMIT 25
```
## Update movie nodes to convert cast array into cast nodes with relationship + convert imdb serialised into imdb nodes
```cypher
MATCH (m:Movie)

WHERE m.json IS NOT NULL

foreach(actor in m.cast | MERGE (c:Cast{name:actor}) MERGE (c)<-[:Casts]-(m))

WITH apoc.convert.getJsonPropertyMap(m, "json") AS j, m

MERGE (i:Imdb{rating:j.rating, votes:j.votes, id:j.id})

MERGE (c)-[:Rated_As]->(i)

REMOVE m.json, m.cast
```
