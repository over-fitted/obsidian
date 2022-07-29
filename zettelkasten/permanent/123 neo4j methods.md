Split between neo4j drivers and spring boot method.

# Neo4j Drivers
### Pros
- More explicit, less magic (no need accessories)
- Simpler, no need to rely on Spring boot
- More apparent control out of the gate with transactions and sessions
- since Tigergraph also does not have OGM, manual mapping of nodes to custom classes logic could be shared between them

### Cons
- Need to use another library's [[OGM]] if want to map POJO objects to data model

# Spring Boot data
### Pros
- Easier to learn for those used to JPA
- More magic, may not even need Cypher knowledge

### Cons
- Presence of outdated or lackluster documentation -> wasted a lot of time finding up to date material that is informative
- High reliance on magic creates additional complexity rather than starting from simple easy to learn ideas to create the complexity on the user side
	- The documentation is really long, not one-to-one with any cypher knowledge, and all of it is necessary out of the gate
- Reliance on spring boot


### Links
- [[120 Cypher Workflow]]
- [[405 Spring Boot Data for Neo4j]]

---

Tags: [[neo4j notes]], [[neo4j code]] 

Date: 2022-06-03

---
