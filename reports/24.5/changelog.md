- Continued to configure java environment
	- finally got system to recognised source folder and run file
# Pain points
- Was using deprecated features of gradle and needed to add `--warning-mode all` to find what features were the issue
	- however, intellij made it very hard to add this to the build. Whenever I changed the build configuration intellij would just disregard it and create a new build configuration, very frustrating
	- structure of project made it very confusing as to how to run the gradlew commands from cli