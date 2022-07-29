- [[121 Sessions]] used to manage transaction causal sequencing
- transactions can be done with [auto commit](https://neo4j.com/docs/java-manual/current/terminology/#term-auto-commit) which wraps singleton queries in a transaction
- Result summary can be obtained with `EXPLAIN` or `PROFILE` query
	- https://neo4j.com/docs/cypher-manual/4.4/query-tuning/how-do-i-profile-a-query/
## Bookmarks
- used as "checkpoint" where server blocks until all prerequisite transactions committed
- passed between sequential transactions in a session automatically
- can be passed between sessions for causal consistency
## making queries
- queries done by sending query text with optional named parameters using tx.run![[Pasted image 20220602160827.png]]
## making transactions with access modes
1. Get session from driver
	1. [can be for different databases if enterprise edition](![[Pasted image 20220602161146.png]])
2. create a write or read transaction with the session
3. choose auto-commit or manual transactions
	1. manual: session.writeTransaction(tx -> prevStepFn(tx, params));
	3. auto-commit: session.run(queryString, parameters(...))
## [consuming results](https://neo4j.com/docs/java-manual/current/cypher-workflow/#java-driver-type-mapping)
- query return type: [Results](https://neo4j.com/docs/api/python-driver/current/api.html#neo4j.Result) - stream of [Records](https://neo4j.com/docs/api/python-driver/current/api.html#neo4j.Result)
	- records are key:value pairs
- Record.get(fieldIndex) -> value type
- coerce value into desired type

---

Source: https://neo4j.com/docs/java-manual/current/cypher-workflow/

Tags: [[neo4j notes]]

Date: 2022-06-02

---
