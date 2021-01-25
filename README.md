# docker_oneliners
Collection of docker oneliners to save time when compiling/running projects for security research &amp; development.

# Compiling Java
## Maven
Compile project in current directory from pom.xml and output to ./target/result.jar
```
docker run -v $(pwd):/usr/src/app maven:3.5-jdk-8 mvn -f /usr/src/app/pom.xml clean package
```
