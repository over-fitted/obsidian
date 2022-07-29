```toc
```
## unwind
- converts a list into a row of items
	- row can then be used with aggregator functions such as collect, and row-modifiers like distinct
## yield
- binds named items to corresponding name
	- rename using `as`
## JSON processing
- add json file to import folder of project
	- click on elipses button next to db, go to `open folder` > `import`
- import using `apoc.load.json("file:///<filename>")`
- yield value, unwind it and operate on rows
- if property value is null, property is skipped
---
## Nested Values
### json serialise
https://neo4j.com/labs/apoc/4.1/overview/apoc.convert/apoc.convert.setJsonProperty/#_input_parameters
- use `apoc.convert.setJsonProperty` to convert map into stringified json
- use [apoc.convert.getJsonPropertyMap](https://neo4j.com/labs/apoc/4.1/overview/apoc.convert/apoc.convert.getJsonPropertyMap/) to convert back
### Parameterisation
- found that $ can be used to set map as parameters, effectively unpacking it into corresponding properties in node
	- https://neo4j.com/docs/cypher-manual/current/syntax/parameters/#cypher-parameters-create-multiple-nodes-with-properties
---
## Merging
- Merging does not create the new node if the properties of the new node are a subset of at least one existing node
- Can set properties on create or on match
	- if want to force-overwrite need to manually write both cases
---
## Configs TODO
https://neo4j.com/docs/java-manual/current/client-applications/
- can configure:
	- security
	- connectionpool size/lifetime
	- connection timeout
	-  resolver
		- multi-database situtation, act as load balancer
	- logging
		- `https://neo4j.com/docs/java-manual/current/client-applications/`
---
## Fulltext search
`CALL db.index.fulltext.createNodeIndex("titlesAndDescriptions",["Movie", "Book"],["title", "description", "review"])`
Create index on nodes (args[1]) and relevant properties (args[2]), assign index to a name (args[1]) 

`CALL db.index.fulltext.queryNodes("titlesAndDescriptions", "Full Metal Jacket") YIELD node, score RETURN node.title, node.review, score`