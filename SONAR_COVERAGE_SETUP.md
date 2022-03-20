
# Adding Code Coverage to SonarCloud Analysis

 This file explain how include test coverage metrics in your project Sonar Quality Gate. 
 Because SonarCloud Automatic Analysis - default value - doesn't include tests executions, we must launch the sonar scanner 
 in our CI proccess and send execution reports to sonarcloud

## 1.Deactivate Automatic Analysis
First step is deactivate  Automatic SonarCloud Project Analysis method. So that, go to your project **dashboard -> administration-> analysis method**
set the value from on to off, as show in the images.

<div align="center"><img width="900" alt="Project DashBoard"  src="https://user-images.githubusercontent.com/29120610/159177341-8af4d379-e4c2-479f-a2e3-d6a10fd363cd.png">
</div>
<div align="center"><img width="900" alt="Project Analysis methods" src="https://user-images.githubusercontent.com/29120610/159177435-07dc1081-2bdf-4cff-b050-fd6e615d1689.png">
</div>

## 2.Create a GitHub Secret
In your GitHub repository, go to **Settings > Secrets** and create a new secret with the following details:
1. In the Name field, enter SONAR_TOKEN
2. In the Value file, enter the value you can retrieve from your SonarCloud Project in **Administration -> Analysis Method -> Github Actions Tutorial**

## 3.Update Github Actions configuration file
In your **.github/workflows/asw2122.yml** change **unit-test-webapp** job to following:
```
unit-test-webapp:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: webapp
    steps:
    - uses: actions/checkout@v2
    - uses: actions/setup-node@v2
      with:
        node-version: 16
    - run: npm ci
    - run: npm test --coverage --watchAll
    - name: Analyze with SonarCloud
      uses: sonarsource/sonarcloud-github-action@master
      env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
    - uses: codecov/codecov-action@v2

```
and the unit-test-restapi to following 
```
unit-test-restapi:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: restapi
    steps:
    - uses: actions/checkout@v2
    - uses: actions/setup-node@v2
      with:
        node-version: 16
    - run: npm ci
    - run: npm test -coverage --watchAll
    - name: Analyze with SonarCloud
      uses: sonarsource/sonarcloud-github-action@master
      env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
    - uses: codecov/codecov-action@v2
  ```
## 4.Create a sonar-project.properties file

Include a sonar-project.properties with following properties:
```
sonar.organization=arquisoft
sonar.host.url=https://sonarcloud.io
sonar.projectKey=Arquisoft_dede_0

sonar.language=ts
sonar.projectName=asw2122_0
sonar.projectVersion=1.0.4

sonar.coverage.exclusions=**/*.test.tsx,**/*.test.ts
sonar.sources=webapp/src/components,restapi
sonar.sourceEncoding=UTF-8
sonar.exclusions=node_modules/**
sonar.typescript.lcov.reportPaths=**/coverage/lcov.info

```
## 5.Launch workflow

Launch Github Action workflow file and check that sonar include your test coverage
<div align="center"><img width="900" alt="CoverageReport" src="https://user-images.githubusercontent.com/29120610/159177930-dba6f58e-c00f-4ea2-b453-22ffafd8b9cd.png">
</div>


