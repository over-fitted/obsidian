# Autocloseable interface 
- Has close method, auto-close at end of try block
	- no need finally at end of try block to close the thing
	- if dont close, may have resource  - inefficient/deadlock
```java 
try (FileReader fr = new FileReader(path);
	 BufferedReader br = new BufferedReader(fr)) {
	return br.readLine();
}
```
***
# Bean 
instance of class managed by spring IoC container

***
# Controller-Service-Repository
https://www.baeldung.com/spring-component-repository-service
Separation of Concerns pattern in Spring boot applications. Lends well to testing where we can mock the other layers.
### Controller
- Closest to external entities
- Exposes functionalities to be consumed
	- should be lean
	- passes responsibilities down to service layer
### Service
- Business logic
### Repository
- stores and receives data
***
