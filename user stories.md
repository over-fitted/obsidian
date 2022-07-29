### Simple CRUD

#### Create
1.  Add a node with properties of the following types:
	1. list
	2. map 
		1. see what is possible for handling maps
	3. number
	4. string
2. Add relationships with the same property types as nodes
3. Write custom queries to programmatically create nodes from nested JSON input exported from mongodb
4. Write custom queries to programmatically create relationships from nested JSON input exported from mongodb
5. Write custom queries to programmatically create nodes from CSV input
6. Write custom queries to programmatically create relationships from csv input

#### Read

1. Given a relationship pattern, I want to find all instances of the pattern
	1. regardless of direction or labels attached to relationship and node
	2. that include a given set of node and relationship labels
2. Given a pair of node identifiers, I want to find all relationships that exist between the corresponding pair of nodes
3. Given a value of a relationship property, I want to find out information of the relationship
4. Given a value of a node property, I want to find out information of the node

#### Update
1. Given a pre-existing set of nodes and a conditional filter such as by node-type, I want to change the value of a property of each of the nodes returned by the filter.
2. Given a pre-existing set of nodes and a conditional filter, I want to add a property to each of the nodes returned by the filter
	1. note down whether there is a need to migrate the rest of the data such as in the case of the presence of a schema - test for dbms agility to changing use-cases
3. Given a pre-existing set of nodes and a conditional filter, I want to remove a property from each of the nodes returned by the filter.

#### Delete

1.  Remove a relationship
2. Remove a node and all relationships attached to that node
3. Given a conditional filter, remove all nodes returned by the filter, and all relationships attached to those nodes.

### Traversals (Arbitrary number of hops over edge collections)

1.  Given a node, I want to find all nodes up to n degrees of separation away from the node regardless of relationship types.
2. Given a node and a set of relationship types, I want to find all nodes up to n degrees of separation away from the node under the condition that relationships involved in the corresponding paths are within the set of relationship types.
	- `(:person)-[:type1|type2*least..most]->()`

### Nested attributes
1. Given nested attributes as exported from a document-oriented database, I want to process the attributes via a manual method (i.e. using custom queries) in a manner that lends well to graph database use-cases as per the following:
	1. Maps
	2. Serialised maps
	3. Lists
	4. Flattened set of properties
	5. New nodes connected to the parent node by relationships corresponding to the property
2. Given nested attributes as exported from a document-oriented database, I want to automatically insert the data into the graph database with little additional input. (unlikely)

### Cross-Domain Queries
1. Given a relationship pattern that includes nodes of different types, I want to find all instances of the pattern with nodes involved corresponding to the types given.

### Fuzzy Matching
1. Given a query string, I want to find all nodes that have property values that are similar to that query string.

