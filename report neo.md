- Can use lucene natively using APOC methods, but creation of index needs to be done manually with custom index, with index name as magic constants
- Error can happen if repository return type is different from query return type
	- optional vs list, need to limit correctly

# Search Functionality
# Schema
- Schemaless = no data type integrity checks
- When paired with strongly-typed POJO properties, will have coercion errors when data types differ
- Errors are generally not helpful, with some red herring tips and inability to find what the problem data is
![[Pasted image 20220617165515.png]]
    - Red herring example is when using java Long to represent Neo4j LONG when equivalent should be java Double. Error message would reference neo4j LONG and java string which is misleading.

# Methods for querying

## Static compile-time query using apoc.paths.expandConfig
- config allows specification of path length range using parameters, in addition to black and whitelist
- Negatives
    - expandConfig has to finish running in its entirety before WHERE filters can be applied
    - specifying expandConfig limit may result in less nodes than expected as these limited nodes are then further filtered
        - specifying limit after WHERE only optimises the WHERE filtering rather than the traversal. Finding all paths using limit after WHERE is infeasible with memory limit
# Paths


# Performance
- USING NEO4J DESKTOP, when a long query is issued, all transactions are rejected until the query is completed, cannot restart db to circumvent
    - may be different if neo4j cli is used instead