# Basic Queries
## Requirements
- [x] Find all of the nodes of that type
- [x]  Find one node matching a property
- [x] Find all based on 1 property equality
- [ ] Find all based on 1 property inequality
- [ ] Find all based on 2 properties
- [ ] Return sorted list ASC and DESC
- [x] Return one node property other than that used as the key
- [ ] Return subset of node properties (projection)
- [x] Search by Regex
- [ ] Search case insensitive
- [ ] Search by containing the keyword
- [ ] Search by similarity to search key
- [ ] Find relationship based on source and sink
- [x] Add limit based on endpoint argument

#### Actual queries
- [x] Find all movies
- [x] Find plot of one movie using name of the movie
- [x] Find movie by title with regex
- [ ] Find user's comment on a movie

***
# Hops
- [x] one-hop to child with only one parent 
	- e.g. nested property
- [x] one-hop to child with multiple parents
- [x] 2-hop with label-restrictions on relationships
- [x] 2-hop with label-restrictions on nodes
- [x] kd hop with label-restrictions on relationships and nodes
- [x] Show path comprising of only one type of node, with edges, given that root node of edges are the queried nodes
- [x] show path with one type of node... given that root node of edges are NOT the queried nodes
- [x] Show path comprising multiple types of nodes
- [x] Show path with edges containing data

#### Actual queries
- [ ] Find movies whose IMDB rating is above supplied integer argument sorted by imdb score
- [ ] Find movies that share the same director as a given root node
	- 2-hop using multi-level projection
	- Returns nodes
- [ ] Find cast members shared by 2 supplied movies
- [x] Find shortest path between 2 cast members
- [ ] Find shortest path between 2 movies based on cast members, only nodes
- [ ] Find edges used in the shortest path, based on results given in the previous task
	- Programmatic cypher query generation
- [ ] Find both nodes and edges used in the shortest path in the same query
- [ ] Find actors whose shortest path is within range k..d
- [ ] Find shortest path between 2 movies based on commenting users, just user and movie nodes
- [ ] Find shortest path between 2 movies based on commenting users with edges data
***
# Related queries
- [x] Find closest nodes to a given root node based on number of shared nodes
#### Actual
- [ ] Find actors in descending order of how many films they have acted together with a root actor (e.g. find close connections to Kevin Spacey)
***
# Extensions
- [ ] Full-text Search
- [ ] Visualisation on front-end 

#### Actual Queries
- [ ] Find Movies whose plot matches the keywords given