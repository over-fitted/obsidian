# Clarification
- should i fix the autowire for services
- what return type for the API
    - can define as we go through interpretations
- Should we try all permutations, or just the sequence as provided
# Interpretations
#### Mixed node classes (cross-domain) traversals
`MATCH p = (:Cast)-[*$a..$b]-(:Cast) return p limit $c`
- No filter on casts, no filter on edges traversed, set a limit on path size and number of paths returned to reduce computation time for demo
- since focus is on how casts know each other, we should return each path individually, rather than just returning hydrated nodes
- TO CLARIFY: how do we represent the paths when returning
    - there may be multiple edges between 2 nodes, and these edges may have the same label
        - e.g. multiple comments
    - possible methods:
        - arraylist of strings, where each string is a path containing node IDs. Nodes can be from multiple domain classes/labels since all nodes have a UUID
        - same as above, but with edges between each node ID
            - can represent each edge as Comment>, thereby showing edge label and direction. This would not show the contents of the edge, which would be confusing when there are duplicates as with comments
        - arraylist of arraylist of domain objects without edges
            - have an endpoint that converts list of strings from first method into corresponding objects
            - can also have another endpoint to find all edges between 2 nodes in a 1-hop path
                - can use this to derive second method from first method
Endpoint: `/cast/allRel/{minRange}/{maxRange}/{limit}`
Data:
list of strings, with each string representing a path containing only IDs of nodes traversed

#### Mixed node classes (cross-domain) traversals with same node class filters
```cypher
MATCH p = (cast1:Cast)-[*$a..$b]-(cast2:Cast)-[*$a..$b]-(cast3:Cast)
WHERE cast1.name = $d AND cast2.name = $e AND cast3.name = $f
return p limit $c`
```
- ability to specify property constraints to find desired nodes, and force the path to go through these nodes
- can have any number of cast#
- NOT specifying what nodes we are allowed to traverse in `-[*$a..$b]-`
- length of total path is not specified, length of path between each pair of nodes follows the same range i.e. `$a..$b`
    - tigergraph may or may not be able to specify minimum

Endpoint: `/cast/path/castNames/{minRange}/{maxRange}/{limit}/..AnyNumberOfNamesSeparatedBySlashes`
Data:
list of strings, with each string representing a path containing only IDs of nodes traversed. Each path should pass through nodes corresponding to names specified

#### Mixed node classes (cross-domain) traversals with mixed node class filters
```cypher
MATCH p = (node1)-[*$a..$b]-(node2)-[*$a..$b]-(node3) 
WHERE node1.id = $id1 AND node2.id = $id2 AND node3.id = $id3 
RETURN p
```
- Same filter should be able to apply to nodes of different label
    - i.e. can use the same filter to filter by ID regardless of node label
- TO CLARIFY:
    - Do we know the types of the nodes to be used at compile time, or should we query all labels to find desired ID
        - i.e. should we force node1 to be a specified type, and do we know this type at compile-time
        - knowledge at run-time requires the cypherDSL
    - Should we generate all permutations of A,B,C or can we just use the ordering given
Endpoint: `/path/mixed/{minRange}/{maxRange}/{limit}/..AnyNumberOfIDsSeparatedBySlashes`
Data:
list of strings, with each string representing a path containing only IDs of nodes traversed. Each path should pass through nodes corresponding to ids specified

#### Mixed node classes (cross-domain) traversals with nested attributes filter
- Specify the nodes that we can traverse to find all paths between every pair of casts
- specification must reference a nested attribute as indicated in stories
- specification does not refer to the cast nodes traversed since we are finding all pairs of casts

Endpoint: `/cast/path/nested/tomato/{minRange}/{maxRange}/{limit}/minTomatoScore`
Endpoint: `/cast/path/nested/awards/{minRange}/{maxRange}/{limit}/minAwards`
Endpoint: `/cast/path/nested/rating/{minRange}/{maxRange}/{limit}/minRating`

#### Mixed node classes (cross-domain) traversals with same node class and nested attributes filters
- same as above, but we can specify a set cast nodes we want the path to necessarily traverse through
- specification for nodes that must be in traversal must reference the same type of label i.e. casts
- this label is known at compile time and is endpoint-specific

Endpoint: `/cast/path/nested/named/{minRange}/{maxRange}/{limit}/minAwards/..AnyNumberOfNamesSeparatedBySlashes`

#### Mixed node classes (cross-domain) traversals
- Idea is to specify a repeatable pattern that should span the path which contains a set of specified nodes. This repeatable pattern is hardcoded and endpoint-specific.
- repeatable pattern is `(:Movie)-[:Casts]->(:Cast)<-[:Casts]-(:Movie)`
    - may want to use a variety of edges, else I can do `(:Movie)-[:Casts*$a..$b]-(:Movie)`
- specification is on both movie and cast nodes that must be traversed by the path, based on ID as per mixed node filter
    - this part is not trivial
- NOT specifiy the number of times the pattern appears in the path
- NO filter on the non-specified nodes, i.e. no whitelist, no "All nodes traversed must have tomato score above x"
- 
Endpoint: `/cast/path/nested/named/{minRange}/{maxRange}/{limit}/minAwards/..AnyNumberOfIDsSeparatedBySlashes`

#### Mixed node classes (cross-domain) traversals with same node class filters
`MATCH p = (a)-[:Casts*minRange..maxRange]-(b) WHERE a.title = $title1 AND b.title = $title2 RETURN p`
- ONLY SOURCE AND SINK specfied
- source and sink are of same label, and both specified using the same property but with different values
    - i.e. specify source and sink using titles
- Add range of path length
- intermediary nodes must be of type movie or cast
    - already handled by Cast edge only being between movie and cast
- no additional filter applied to intermediary nodes
    -  i.e. no whitelist, no "All nodes traversed must have tomato score above x
Endpoint: `/path/casts/{minRange}/{maxRange}/{limit}/..AnyNumberOfIDsSeparatedBySlashes`

#### As a user, I want to find out the **direct links between casts and movies for Cast B and Cast C**, to find out how they know each other through movies
Unclear interpretation
1. Shortest path
2. enfoce that it must be `(a:Cast)<-[:Casts]-(m:Movie)-[:Casts]->(b:Cast) WHERE a.name = $name1...`
    1. i.e. only 1 movie between the 2 cast nodes