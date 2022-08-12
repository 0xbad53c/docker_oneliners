# docker_oneliners
Collection of docker oneliners to save time when compiling/running tools/projects for security research &amp; development. Credits for the projects go to the original owners. This repository just aims to ease usage for myself.

# Compiling Java
## Maven
Compile project in current directory from pom.xml and output to ./target/result.jar. Easy way to change java or maven version without having multiple Java versions on your system!
```
docker run -v ~/.m2:/root/.m2 -v $(pwd):/usr/src/app maven:3.5-jdk-8 mvn -f /usr/src/app/pom.xml clean package install
```


# Automatic scanning for secrets/SAST/...
## GitLeaks
https://github.com/zricethezav/gitleaks
Tool to hunt for secret leaks in your offline repository. Output will be printed to terminal or write it to file with --report=

### Oneliner
```
docker run --rm -v "$(pwd)":/my-repo zricethezav/gitleaks:latest --path="/my-repo";
```

### Scan multiple project directories at once
Drop the projects all in the same folder and run the oneliner from the folder.
```
find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c 'cd "{}" && docker run --rm -v "$(pwd)":/my-repo zricethezav/gitleaks:latest --path="/my-repo"' \;
```

## ShiftLeft Sast-scan
https://github.com/ShiftLeftSecurity/sast-scan
This is a free open-source security tool which can detect various kinds of security flaws in your application, and infrastructure code in a single scan. It bundles various other open-source tools and will perform things like SAST, check for secrets and even scan infrastructure-as-code for best practices. it outputs HTML/JSON reports to the reports folder.

### Oneliner
```
docker run --rm -e "WORKSPACE=$(pwd)" -v "$(pwd)":/app shiftleft/sast-scan scan --build
```

## Scan multiple project directories at once
Drop the projects all in the same folder and run the oneliner from the folder.
```
find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c 'cd "{}" && docker run --rm -e "WORKSPACE=$(pwd)" -v "$(pwd)":/app shiftleft/sast-scan scan --build' \;
```

## Trufflehog
https://github.com/trufflesecurity/trufflehog
Tool to hunt for secret leaks in your offline repository. Output will be printed to terminal.

### Oneliner
```
docker run --rm -v "$(pwd):/proj" dxa4481/trufflehog file:///proj
```

### Scan multiple project directories at once
Drop the projects all in the same folder and run the oneliner from the folder.
```
find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c 'cd "{}" && docker run --rm -v "$(pwd):/proj" dxa4481/trufflehog file:///proj' \; >> trufflehog.txt
```

## Semgrep
https://github.com/returntocorp/semgrep
Semgrep is a fast, open-source, static analysis tool for finding bugs and enforcing code standards.

### Oneliner
```
docker run --rm -v "$(pwd):/src" returntocorp/semgrep --config=p/security-audit /src
```

### Scan multiple project directories at once
Drop the projects all in the same folder and run the oneliner from the folder.
```
find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c 'cd "{}" && docker run --rm -v "$(pwd):/src" returntocorp/semgrep --config=p/security-audit /src' \;
```

## Dependency Confusion scanning with Confused (build docker container first)
Original repo: https://github.com/visma-prodsec/confused
Repo with Dockerfile until pull request is accepted: https://github.com/0xbad53c/confused
Tool to check for dependency confusion issues.

### Oneliner
```
docker run --rm -v "$(pwd):/src" confused -l npm /src/package.json
docker run --rm -v "$(pwd):/src" confused -l mvn /src/pom.xml
```

### Scan all package.json and pom.xml files in subdirectories
```
find . -name package.json -exec bash -c 'cd "$(dirname {})" && echo "testing {}" && docker run --rm -v "$(pwd):/src" confused -l npm /src/package.json' \;
find . -name pom.xml -exec bash -c 'cd "$(dirname {})" && echo "testing {}" && docker run --rm -v "$(pwd):/src" confused -l mvn /src/pom.xml' \;
```

## Snyk
Tool to scan for vulnerable dependencies.
Create a free account at https://snyk.io and fetch the API key from your profile. This can be used to scan your projects. Outputs a JSON with all vulnerable dependencies, which can be converted to a HTML report with custom template with snyk-to-html. Snyk has containers for many dependency managers at https://hub.docker.com/r/snyk/snyk.
snyk-to-html can also be built as Docker container. The Dockerfile is included in https://github.com/snyk/snyk-to-html

### Scan Gradle project with Snyk
```
docker run --rm -it --env SNYK_TOKEN=<YOUR API KEY> -v "$(pwd)":/app -v "$(pwd)/.gradle":/home/gradle/.gradle snyk/snyk:gradle "snyk test -d --all-projects --json-file-output=snyk-test-output.json"
snyk-to-html -i snyk-test-output.json -o snyk-result.html -t ~/snyk-to-html-template/template/test-report.hbs
```

## SSL/TLS testing
```
docker run --rm -ti drwetter/testssl.sh https://www.example.com
docker run --rm -it nablac0d3/sslyze www.example.com
```
