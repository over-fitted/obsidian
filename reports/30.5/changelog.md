# Changelog
- Learned basics of Spring Boot and Neo4j with Spring
- Connected demo PoC to Neo4j database with proof that CRUD works
- Followed https://spring.io/guides/gs/accessing-data-neo4j/
# Issues
- bidirectional query from the guide does not work
- guide covers only querying with `org.springframework.data.neo4j.repository.Neo4jRepository`
	- I want to use cypher first so will need to look into that

# Misc. Ideas/thought
- What's the difference between [[Controller-Service-Repository]] and [[MVC]]?
- Java and spring boot are painful. Feels like there is a lot of stuff thrown at us up-front, and trying to avoid spring boot to focus on Neo4j seems like a bad idea since spring has some methods  and functionalities that should be incorporated from the beginning
- Many neo4j-spring tutorials seem to assume knowledge of JPA (Jakarta Persistence API), and JPA appears to be an alternative to JDBC. Accessories in neo4j driver seem to be modelled after JPA's like NodeEntity modelled after Entity. Feel like I spent a lot of today lost in figuring out what each term means. We should probably create a glossary and repo of useful java docs so future interns would hopefully not get as lost in the sauce.
- Java accessories is pain - feels too magic with a lot of abstraction forcing us to be overreliant on potentially outdated documentation (e.g. nodeEntity is now replaced with node)
- Zettelkasten seems interesting for handling large amounts of data that does not require strict clustering. This would take the form of misc temporary ideas, resource summaries or permanent notes.
	- Strict clustering = all files must be in same folder, info kept as clustered as possible, usually when they are actively used for a current project
	- Zettelkasten benefit is instead to keep track of less-critical info that can be stored for future use, particularly if the info is one-off or not critical to current use-cases
		- Zettels then provide a means of writing accessible atomic notes of non-obvious use + providing useful metadata for querying
		- Tags can be used to shortlist potentially useful zettels in the future by organically generating the resultant knowledge graph
	- May be useful for tracking miscellaneous information such as JDBC, mongodb or connection pool-related knowledge
# Todo
- Work on getting Cypher PoC to work
- in the next stage to read this https://docs.spring.io/spring-framework/docs/3.0.0.M4/spring-framework-reference/html/index.html