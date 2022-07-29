# User Community
## Community
Neo4j official forums have questions largely about debugging and how to achieve certain tasks both in Cypher and with related tools such as Spring Data. These questions tend to have around 100 views and have replies from about 2 users within the day for reasonably specific or easy to understand questions. These replies would come from the Neo4j community team or from the user community, whose activity in the forums would be denoted by a rank-like desination like "ninja". 

Issues raised on the forums span a wide range of topics across language and use-cases. Basic cypher-related queries seem to be sufficiently well-answered on the forums. However, for Spring Data-related and more complicated non-standard use-case queries, several answers seem to be outdated or point to documentation that is generally too vague to be helpful. Same would apply to StackOverflow posts.

Neo4j has a discord server with neo4j team members, but queries largely seem to go unanswered. They also hold regular meetups and conferences, but I have yet to attend a live one. I have not attempted to use 

Official support is only available to customers via a support portal or through direct contact with professional services.

Pros:
- Sufficient activity on community sites to provide leads on most basic queries I have where I have not yet needed to post queries.
- Reasonable expectation to find an answer if a sufficiently well-crafted post is formed

Cons:
- Outdated queries are not obviously marked outside of time-stamp which may lead to wasting time with a non-functional fix. This is exacerbated by large changes in Spring Data with Neo4j rendering many answers useless

# Developer Experience
## DB experimentation
Cypher queries can be tested easily on Neo4j Desktop, before attempting to adapt the queries to other platforms such as Java middleware. Data can also be interacted with via a GUI which provides a visual way to experiment with the data.

Spinning up new DB instances is handled by the application so test DBs can be created with sample data for experimentation with new approaches.

## API Development
Neo4j itself has a HTTP endpoint but it was out of scope for the internship as the focus was on using Java drivers directly with Neo4j.

As Neo4j itself is written in Java, Java seems to be the preferred approach for api development, appearing first under Drivers and Language guides in the Developer Guides and being the language used to write custom user-defined procedures as per Java reference. However, other languages also seem to be officially supported as with Golang and Python.

With Java, several approaches are offered through the Java Neo4j Driver, Spring Data for Neo4j and CypherDSL, where the preferred method is not obvious from the documentation. Spring Data for Neo4j appears to be sufficiently robust for most use-cases, where developers can generally overcome its shortcomings using creative approaches such as list-comprehension, pre-computation, or using Java to break the problem down into simpler neo4j queries which can then be processed in Java to form useful output.

When using Spring Data, experienced developers may be able to work quickly relying upon magic provided by Spring such as autowiring and object graph mapping, the latter automatically mapping data of nodes and edges to POJOs.

However, there are many features and edge cases that are not well-documented and can only be understood by experimentation as a result of this magic. For example, custom labels and relationships took me a while to experiment with.

## GUI
GUI provides interactivity with data, allowing user to expand nodes to view neighbours which may not be in the return paths or inspect individual entities in detail. GUI also allows user to shift nodes around.

However, GUI tends to heavily lag when large amounts of data are returned, even when the graph view is not selected.

## Erroneous Use
Neo4j Desktop
- Error messages highlight where erroneous code may be present, and provide hints as to what may have been expected in the location highlighted. Linting is also present which reduces the chance of typos or mis-use of functions.
- Error messages may sometimes be misleading or makes reference to underlying Java. Also as Cypher is a declarative language, errors are generally in terms of syntax so usefulness of error messages are limited.
- Should too much data be returned, the entire application tends to lag and require restart.
##### Spring Data
- Syntax errors in static Cypher queries cannot be recognised by Intellij linter, so Neo4j Desktop is recommended for testing queries before transferring to Spring Data. Further errors can only be caught at runtime, but they tend to be of similar quality to those thrown in Neo4j Desktop.
- Errors in domain modelling are hard to debug since there are no actual errors, and since Object Graph Mapping is automatic we have no code to trace. This is exacerbated by many new features being Neo-specific such as @Relationship annotations, and the presence of annotation fields whose precise use can only be found in Javadocs.

# Learning Curve
##### Guides and Documentation
###### Cypher
Official online docs and guides are segregated between Developer Guide, Neo4j Cypher Manual and Java Reference. Other resources are also recommended by the [Developer guide](https://neo4j.com/developer/getting-started-resources/), of which I tried the OReilly textbook. Separately, a getting starter guide is provided within Neo4j Desktop.

The developer guide can be hit or miss. It provides a general overview about many topics and provides links to more detailed documentation about these topics. However, the developer guide tends to skim a lot of detail and is of lower quality than linked documentation. It may also provide outdated information outside of Cypher with warnings only sometimes provided, as with mongo-connector docs.

I would recommend following the guide so long as it makes sense as it tends to provide sample code with diagrams which is friendly to beginners to follow along. However I would also recommend immediately leaving the guide in favour of linked documentation once it starts to feel overly detailed about non-relevant material. This is particularly the case for non-cypher-related material.

The Cypher Manual is of high quality but functions more like documentation than a guide, as it goes into detail about all standard features of Cypher on a feature-by-feature basis, but does not provide an overarching narrative to understand the significance of each feature, where to an inexperienced user the manual may prove to be an unparsable info-dump and may lead to excessive overreading of features that may not be relevant to ones use-case.

I have not read much into the Java Reference.

The textbook is of high quality and is recommended for providing a high-level overview of the use-cases of Graph Databases and a beginner guided introduction to Cypher, but the Java coding sections are generally deprecated and can be skipped. If context about Neo4j or graph databases are not required, I would recommend skipping to the getting started guide provided in Neo4j Desktop.

Several youtube videos and livestream are provided on Neo4j's youtube channel, however these either tend to emphasise on data science use-cases or on evangelism with high-level overviews, and are not useful in approaching advanced use-cases as a DBMS.

###### Spring Data
Spring Data for Neo4j docs are present in Developer Guide but largely written in a Spring.io doc. The developer guide is of poor quality and should be skipped in favour of the spring.io doc. 

The spring.io doc does not provide a useful getting started guide, and assumes knowledge of Spring Data and Lombok. Code walkthroughs stop short of becoming functional, but can be reasonably followed with Spring Data knowledge. Flow of documentation can be hard to follow since it pushes for Reactive Programming and includes advanced material mixed in with more basic material. Some features are only vaguely covered, with links to non-existent or broken documentation.

3rd-party guides including Dzone and Baeldung are largely outdated and lacking in information. Recommend skipping.

Overall, it is easy to accidentally read outdated documentation/guides, so it is recommended to check versions before investing time into a document. I also feel that it is easy to get lost transitioning from beginner guides to documentation, where it is easy to get lost not knowing what to look for. I personally lost a lot of time not knowing how to do what I wanted to, with relevant forum posts not receiving useful answers likely due to the vague nature of such questions. It is unclear how to overcome this  as it is an issue of methodology rather than documentation.

Documentation for CypherDSL is extremely limited, only providing a cookbook for basic usecases corresponding to the beginner starter guide in neo4j desktop, and otherwise requiring the developer to parse through the Javadocs which are hard to read without guidance, since many workflows involve creating objects using Cypher factory methods and calling methods on those objects.

I would recommend following this order for developing in Spring Data for Neo4j:
Getting Started guide in Neo4j Desktop > Spring in Action > spring.io guide of Spring Data for Neo4j > Neo4j Developer Guide and Cyper manual for reference when doing Cypher coding.
I would also highly recommend getting familiar with Cypher predicate, aggregating and list functions as they are surprisingly useful when attempting more complicated usecases or queries, such as when trying to retrieve data on a path-by-path basis.

##### Query Language
Cypher is very intuitive to understand, utilising ASCII art-like syntax for specifying path patterns. It also bears some semblance to SQL's syntax and approach to coding due to Cypher's declarative nature. It also supports the use of parameters with caching built-in for optimisation. 

However, parameter may be unexpectedly unrecognised in certain places of the query, such as when used to specify path length. APOC documentation also tends to be sparse, where configs are rarely documented with sufficient detail if at all. Example of this can be seen with apoc.path.expandConfig which is used heavily in the project.

CypherDSL and Spring Data default repository methods are not well-documented as indicated in previous sections.

