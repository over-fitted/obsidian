```
MATCH (n:User)-[*]-(m)

WITH n, collect(m) as col

WHERE size(col)<15 and size(col) > 5

RETURN n LIMIT 15
```
```
MATCH (bacon:Movie {title:"Titanic"})-[:Casts*1..5]-(hollywood:Movie)

WITH DISTINCT hollywood as sink

LIMIT 100

WHERE sink.title <> "Titanic" 

MATCH p = shortestpath((:Movie {title:"Titanic"})-[:Casts*1..5]-(sink))

RETURN p
```
## load comments as relationships via apoc
```cypher

CALL apoc.load.json("file:///comments_no_date.json") YIELD value

unwind value as q

MERGE (u:User{email:q.email})

MERGE (m:Movie{id:q.movie_id.oid})

MERGE (u)-[:Commented_On{id:q._id.oid, text:q.text}]->(m)
```
# Find shortest path between 2 actors via movies (Cross-Domain)
```cypher
MATCH p=shortestPath((bacon:Cast{name:"Kevin Miles"})-[*]-(meg:Cast{name:"Bruce Dern"}))

RETURN p
```

# Load Movies
```cypher
CALL apoc.load.json("file:///movies_no_date.json") YIELD value
unwind value as q
MERGE (m:Movie{id:q._id.oid})
SET m.plot = q.plot
foreach(g in q.genres | MERGE(G:Genre{name:g}) MERGE(m)-[:Categorized_As]->(G))
set m.lastUpdated = q.lastUpdated
set m.title = q.title
set m.num_mflix_comments =  q.num_mflix_comments
set m.tomatoes_numReviews = q.tomatoes.viewer.numReviews
set m.tomatoes_rating = q.tomatoes.viewer.rating
set m.tomatoes_meter = q.tomatoes.viewer.meter
set m.awards = apoc.convert.toJson(q.awards)
MERGE(i:Imdb{id:q.imdb.id})
SET i = q.Imdb
MERGE(m)-[:Rated_As]->(i)
foreach(d in q.directors | MERGE(D:Director{name:d}) MERGE (m)-[:Directed_By]->(D))
foreach(c in q.cast | MERGE(C:Cast{name:c}) MERGE (m)-[:Casts]->(C))

  

foreach(w in q.writers | MERGE(W:Writer{name:w}) MERGE (m)-[:Written_By]->(W))
```
# Load users
```cypher
CALL apoc.load.json("file:///users.json") YIELD value

UNWIND value as q

MERGE (u:User{email:q.email})

SET u.name = q.name

SET u.id = q._id.oid

SET u.password = q.password
```


# To Try
```cypher
MATCH p=(m:Movie{title: 'The Matrix'})<-[:ACTED_IN]-(:Person)-[:ACTED_IN*..0]->(:Movie)
WITH collect(p) as paths, m
WITH m,
reduce(a=[], node in reduce(b=[], c in [aa in paths | nodes(aa)] | b + c) | case when node in a then a else a + node end) as nodes,
reduce(d=[], relationship in reduce(e=[], f in [dd in paths | relationships(dd)] | e + f) | case when relationship in d then d else d + relationship end) as relationships
RETURN m, relationships, nodes;
```
```cypher
MATCH p=(m:Movie{title: 'The Matrix'})<-[:ACTED_IN]-(:Person)-[:ACTED_IN*..0]->(:Movie)
RETURN m, nodes(p), relationships(p);
```
# Set all imdb ratings to float, if some are integer
```
match (i:Imdb) set i.rating = apoc.convert.toFloat(i.rating)
```

# Delete imdb with empty ratings
```cypher
match (i:Imdb) where apoc.meta.type(i.votes) <> "INTEGER" DETACH DELETE i
```

# String paths with edges and nodes
```cypher
MATCH p = (:`PathNode` {id: '573a139af29313caabcefb1d'})-[:`Casts`*3..7]-(:`PathNode` {id: '573a1398f29313caabceb515'}) 

RETURN reduce(acc =nodes(p)[0].id + ", ", i IN range(1,length(p)) | acc + TYPE(relationships(p)[i-1])
        + CASE startnode(relationships(p)[i-1])
            WHEN nodes(p)[i-1] THEN ">"
            ELSE "<"
        END
        + ', ' + nodes(p)[i].id + ", "

) LIMIT 15
```

# paths with edges, multiple nodes specified and permutations
```cypher
match p = (m)-[:Casts*7]-(n)

WHERE m.id in ['573a139af29313caabcefb1d',"0530e7b6-c25d-4bf2-bec7-0656a1c395d4", "573a1398f29313caabcead65"]

AND n.id in ['573a139af29313caabcefb1d',"0530e7b6-c25d-4bf2-bec7-0656a1c395d4", "573a1398f29313caabcead65"]

AND ALL(target in ['573a139af29313caabcefb1d',"0530e7b6-c25d-4bf2-bec7-0656a1c395d4", "573a1398f29313caabcead65"] WHERE target in [n in nodes(p) | n.id])

return reduce(b=nodes(p)[0].id + ', ',i in range(1,length(p)) | b + TYPE(relationships(p)[i-1]) + CASE startnode(relationships(p)[i-1]) WHEN nodes(p)[i-1] THEN '>' ELSE '<' END + ', ' + nodes(p)[i].id + ', ')
```

# Story 4a
```cypher
MATCH (n:Cast)-[:Casts]-(m:Movie) 

WHERE m.tomatoes_rating >= 4.9

WITH collect(n) as res 

MATCH (a:Cast) WHERE a in res 

MATCH (b:Cast) WHERE b in res 

CALL apoc.path.expandConfig(a, { 

    minLevel: 1, 

    maxLevel: 3, 

    terminatorNodes: [b], 

    relationshipFilter: "Casts", 

    limit: 2 

}) 

YIELD path as p

return reduce(b=nodes(p)[0].id + ', ',i in range(1,length(p)) | b + TYPE(relationships(p)[i-1]) 

+ CASE startnode(relationships(p)[i-1]) WHEN nodes(p)[i-1] THEN '>' ELSE '<' END

+ ', ' + nodes(p)[i].id + ', ') 

LIMIT 15
```
# Story 4c
```cypher
MATCH (m:Movie)-[:Rated_As]->(i:Imdb) WHERE i.rating >= 9

MATCH (n:Cast)<-[:Casts]-(m) 

WITH collect(n) as res 

MATCH (a:Cast) WHERE a in res 

MATCH (b:Cast) WHERE b in res 

CALL apoc.path.expandConfig(a, { 

    minLevel: 1, 

    maxLevel: 3, 

    terminatorNodes: [b], 

    relationshipFilter: "Casts", 

    limit: 2 

}) 

YIELD path as p

return reduce(b=nodes(p)[0].id + ', ',i in range(1,length(p)) | b + TYPE(relationships(p)[i-1]) 

+ CASE startnode(relationships(p)[i-1]) WHEN nodes(p)[i-1] THEN '>' ELSE '<' END

+ ', ' + nodes(p)[i].id + ', ') 

LIMIT 15
```

# Story 5
```cypher
MATCH (a:Cast) where a.id in $res

MATCH (b:Cast) where b.id in $res

CALL apoc.path.expandConfig(a, { 

    minLevel: 1, 

    maxLevel: 3, 

    terminatorNodes: [b], 

    relationshipFilter: "Casts"

}) 

YIELD path as p

WHERE all(no in [n in nodes(p) WHERE 'Movie' in labels(n)] WHERE apoc.convert.fromJsonMap(no.awards).wins >= $minAwards)

AND ALL(target in $res WHERE target in [n in nodes(p) | n.id])

return  reduce(b=nodes(p)[0].id + ', ',i in range(1,length(p)) | b + TYPE(relationships(p)[i-1]) 

+ CASE startnode(relationships(p)[i-1]) WHEN nodes(p)[i-1] THEN '>' ELSE '<' END

+ ', ' + nodes(p)[i].id + ', ') 

LIMIT 15
```

# Story 6
```cypher
MATCH (a:Cast)

MATCH (b:Cast)

CALL apoc.path.expandConfig(a, { 

    minLevel: 1, 

    maxLevel: 3, 

    terminatorNodes: [b], 

    nodeFilter: "Cast, Movie", 

    limit: 2 

}) 

YIELD path as p

return reduce(b=nodes(p)[0].id + ', ',i in range(1,length(p)) | b + TYPE(relationships(p)[i-1]) 

+ CASE startnode(relationships(p)[i-1]) WHEN nodes(p)[i-1] THEN '>' ELSE '<' END

+ ', ' + nodes(p)[i].id + ', ') 

LIMIT 15
```
# Story 7
```cypher
MATCH (a:Cast) where a.name in [$node1, $node2]

MATCH (b:Cast) where b.name in [$node1, $node2]

CALL apoc.path.expandConfig(a, { 

    minLevel: 1, 

    maxLevel: 5, 

    terminatorNodes: [b], 

    nodeFilter: "Cast, Movie", 

    limit: 2 

}) 

YIELD path as p

return reduce(b=nodes(p)[0].id + ', ',i in range(1,length(p)) | b + TYPE(relationships(p)[i-1]) 

+ CASE startnode(relationships(p)[i-1]) WHEN nodes(p)[i-1] THEN '>' ELSE '<' END

+ ', ' + nodes(p)[i].id + ', ') 

LIMIT 15
```

# Story 8
```Cypher
MATCH p=(a:Cast{name:$node1})--(b)--(c:Cast{name:$node2})

WHERE b:Cast or b:Movie

return reduce(b=nodes(p)[0].id + ', ',i in range(1,length(p)) | b + TYPE(relationships(p)[i-1]) 

+ CASE startnode(relationships(p)[i-1]) WHEN nodes(p)[i-1] THEN '>' ELSE '<' END

+ ', ' + nodes(p)[i].id + ', ')
```