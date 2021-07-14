# docker_oneliners
Collection of docker oneliners to save time when compiling/running projects for security research &amp; development.

# Compiling Java
## Maven
Compile project in current directory from pom.xml and output to ./target/result.jar
```
docker run -v ~/.m2:/root/.m2 -v $(pwd):/usr/src/app maven:3.5-jdk-8 mvn -f /usr/src/app/pom.xml clean package install
```


# Scanning repos for secrets
## offline scanning all projects in current directory
### GitLeaks
```
find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c 'cd "{}" && docker run --rm -v "$(pwd)":/my-repo zricethezav/gitleaks:latest --path="/my-repo"' \;
```
