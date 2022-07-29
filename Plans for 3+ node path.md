### Hard-code 3-node path
Use `(a)-[*]-(b)-[*]-(c)`. This does not allow us to specify path length, and we need to hardcode the number of nodes we want to assert a filter on. However, we can also easily generate paths for all permutations of the nodes as with the following
``` cypher
MATCH p=(a)-[*]-(b)-[*]-(c) 
WHERE a.id in ["first", "second", "third"]
AND b.id in ["first", "second", "third"]
AND c.id in ["first", "second", "third"]
AND a.id <> b.id <> c.id
return p
```
### Form permutations in java
For every valid 3-node path, the path can be broken down into 2-node paths, and any pair of such 2-node paths can be matched so long as all 3 nodes are present in the paths.

1. Use java to generate all sequences of the nodes
2. for each sequence
    1. break the sequence down into a pair of nodes
    2. for each pair of nodes
        1. find all paths between the pair
    3. form all combination of paths between each pair

 Dynamic programming may be used here to speed up the query. We could also add a limit by adding a path counter, and just returning all encountered paths once the limit is reached.

Main concerns are
- stack limit
- several seperate 2-node calls made by java client to neo4j driver, which would incur a lot of overhead

## Cypher DSL
Similar to method 1, but we can dynamically generate our query using the DSL. This allows us to add an arbitrary number of nodes, and be able to set parameters for path length. We can additionally set filters including what properties to filter on, inequality/equality operator and value to compare to.