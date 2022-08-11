# docker_oneliners
Collection of docker oneliners to save time when compiling/running projects for security research &amp; development. Credits for the projects go to the original owners. This repository just aims to ease usage for myself.

# Compiling Java
## Maven
Compile project in current directory from pom.xml and output to ./target/result.jar
```
docker run -v ~/.m2:/root/.m2 -v $(pwd):/usr/src/app maven:3.5-jdk-8 mvn -f /usr/src/app/pom.xml clean package install
```


# Automatic scanning for secrets/SAST/...
## offline scanning all projects in current directory
### GitLeaks
```
find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c 'cd "{}" && docker run --rm -v "$(pwd)":/my-repo zricethezav/gitleaks:latest --path="/my-repo"' \;
```

Output will be printed or pass with --report=


### ShiftLeft Sast-scan
```
find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c 'cd "{}" && docker run --rm -e "WORKSPACE=$(pwd)" -v "$(pwd)":/app shiftleft/sast-scan scan --build' \;
```

output will be in /app/reports

### Trufflehog
```
find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c 'cd "{}" && docker run --rm -v "$(pwd):/proj" dxa4481/trufflehog file:///proj' \; >> trufflehog.txt
```

output will be currentdir/trufflehog.txt

### semgrep
```
find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c 'cd "{}" && docker run --rm -v "$(pwd):/src" returntocorp/semgrep --config=p/security-audit /src' \;
```

### Dependency Confusion scanning with Confused (build docker container first)
Repo with Dockerfile: https://github.com/0xbad53c/confused

#### Scan all package.json files in subdirectories
```
find . -name package.json -exec bash -c 'cd "$(dirname {})" && echo "testing {}" && docker run --rm -v "$(pwd):/src" confused -l npm /src/package.json' \;
find . -name pom.xml -exec bash -c 'cd "$(dirname {})" && echo "testing {}" && docker run --rm -v "$(pwd):/src" confused -l mvn /src/pom.xml' \;
```

## SSL/TLS testing
```
docker run --rm -ti drwetter/testssl.sh https://url
```
