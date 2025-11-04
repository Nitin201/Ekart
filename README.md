# Spring Boot Shopping Cart Web App

# CI-PIPELINE
pipeline {
    agent any

    tools {
        jdk 'jdk-17'
        maven 'maven'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Nitin201/Ekart.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Check Java') {
            steps {
                sh "java -version"
                sh "echo JAVA_HOME = $JAVA_HOME"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                sh """
                    export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
                    export PATH=\$JAVA_HOME/bin:\$PATH

                    \$SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectKey=Ekart \
                    -Dsonar.projectName=Ekart \
                    -Dsonar.sources=. \
                    -Dsonar.java.binaries=target/classes \
                    -Dsonar.host.url=http://13.200.200.247:9000 \
                    -Dsonar.login=squ_696bacdb067dcb4caa9f6db1861304b142aee4e7
                """
            }
        }


        stage('Build Application') {
            steps {
                sh 'mvn clean install -DskipTests=true'
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: '220de2d0-c7db-4bb0-b9e3-27302a2b5090', toolName: 'docker') {
                        sh "docker build -t ekart:latest -f docker/Dockerfile ."
                        sh "docker tag ekart:latest nitin241/ekart:latest"
                        sh "docker push nitin241/ekart:latest"
                    }
                }
            }
        }
         stage('Trigger CD Pipeline') {
            steps {
                build job: "CD_PIPELEINE" , wait: true
            }
        }
    
    }
}

## CD-PIPELINE
pipeline {
    agent any

    stages {
        stage('Docker Deploy To Container') {
            steps {
                script {
                    withDockerRegistry(credentialsId: '220de2d0-c7db-4bb0-b9e3-27302a2b5090', toolName: 'docker') {
                        sh "docker run -d --name Ekart -p 8070:8070 nitin241/ekart:latest"
                    }
                }
            }
        }
    }
}



## AboStarted by user nitin
[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins in /var/lib/jenkins/workspace/CI_pipeline
[Pipeline] {
[Pipeline] tool
[Pipeline] withEnv
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Declarative: Tool Install)
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] }
[Pipeline] // stage
[Pipeline] withEnv
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Git Checkout)
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] withEnv
[Pipeline] {
[Pipeline] git
The recommended git tool is: NONE
No credentials specified
 > git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/CI_pipeline/.git # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url https://github.com/Nitin201/Ekart.git # timeout=10
Fetching upstream changes from https://github.com/Nitin201/Ekart.git
 > git --version # timeout=10
 > git --version # 'git version 2.43.0'
 > git fetch --tags --force --progress -- https://github.com/Nitin201/Ekart.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/main^{commit} # timeout=10
Checking out Revision 5fe26751a472e501ce6939e8644569e35eca965e (refs/remotes/origin/main)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 5fe26751a472e501ce6939e8644569e35eca965e # timeout=10
 > git branch -a -v --no-abbrev # timeout=10
 > git branch -D main # timeout=10
 > git checkout -b main 5fe26751a472e501ce6939e8644569e35eca965e # timeout=10
Commit message: "Update Dockerfile"
 > git rev-list --no-walk 5fe26751a472e501ce6939e8644569e35eca965e # timeout=10
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Compile)
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] withEnv
[Pipeline] {
[Pipeline] sh
+ mvn clean compile
[INFO] Scanning for projects...
[INFO] 
[INFO] ---------------------< com.reljicd:shopping-cart >----------------------
[INFO] Building shopping-cart 0.0.1-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- clean:2.6.1:clean (default-clean) @ shopping-cart ---
[INFO] Deleting /var/lib/jenkins/workspace/CI_pipeline/target
[INFO] 
[INFO] --- jacoco:0.8.6:prepare-agent (jacoco-initialize) @ shopping-cart ---
[INFO] argLine set to -javaagent:/var/lib/jenkins/.m2/repository/org/jacoco/org.jacoco.agent/0.8.6/org.jacoco.agent-0.8.6-runtime.jar=destfile=/var/lib/jenkins/workspace/CI_pipeline/target/jacoco.exec
[INFO] 
[INFO] --- resources:2.6:resources (default-resources) @ shopping-cart ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO] Copying 12 resources
[INFO] 
[INFO] --- compiler:3.1:compile (default-compile) @ shopping-cart ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 23 source files to /var/lib/jenkins/workspace/CI_pipeline/target/classes
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  2.019 s
[INFO] Finished at: 2025-11-04T16:40:03Z
[INFO] ------------------------------------------------------------------------
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Check Java)
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] withEnv
[Pipeline] {
[Pipeline] sh
+ java -version
openjdk version "1.8.0_472"
OpenJDK Runtime Environment (Temurin)(build 1.8.0_472-b08)
OpenJDK 64-Bit Server VM (Temurin)(build 25.472-b08, mixed mode)
[Pipeline] sh
+ echo JAVA_HOME = /var/lib/jenkins/tools/hudson.model.JDK/jdk-17
JAVA_HOME = /var/lib/jenkins/tools/hudson.model.JDK/jdk-17
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (SonarQube Analysis)
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] withEnv
[Pipeline] {
[Pipeline] sh
+ export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
+ export PATH=/usr/lib/jvm/java-17-openjdk-amd64/bin:/var/lib/jenkins/tools/hudson.model.JDK/jdk-17/bin:/var/lib/jenkins/tools/hudson.tasks.Maven_MavenInstallation/maven/bin:/var/lib/jenkins/tools/hudson.model.JDK/jdk-17/bin:/var/lib/jenkins/tools/hudson.tasks.Maven_MavenInstallation/maven/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/snap/bin
+ /var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/sonar-scanner/bin/sonar-scanner -Dsonar.projectKey=Ekart -Dsonar.projectName=Ekart -Dsonar.sources=. -Dsonar.java.binaries=target/classes -Dsonar.host.url=http://13.200.200.247:9000 -Dsonar.login=squ_696bacdb067dcb4caa9f6db1861304b142aee4e7
16:40:05.854 INFO  Scanner configuration file: /var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/sonar-scanner/conf/sonar-scanner.properties
16:40:05.860 INFO  Project root configuration file: NONE
16:40:05.872 INFO  SonarScanner CLI 7.3.0.5189
16:40:05.874 INFO  Linux 6.14.0-1016-aws amd64
16:40:06.876 INFO  Communicating with SonarQube Server 9.9.8.100196
16:40:07.354 INFO  Load global settings
16:40:07.425 INFO  Load global settings (done) | time=71ms
16:40:07.426 INFO  Server id: 147B411E-AZpOXm7uu8UQoTw_ELXv
16:40:07.429 INFO  User cache: /var/lib/jenkins/.sonar/cache
16:40:07.432 INFO  Load/download plugins
16:40:07.432 INFO  Load plugins index
16:40:07.468 INFO  Load plugins index (done) | time=36ms
16:40:07.523 INFO  Load/download plugins (done) | time=91ms
16:40:08.058 INFO  Process project properties
16:40:08.067 INFO  Process project properties (done) | time=9ms
16:40:08.069 INFO  Execute project builders
16:40:08.070 INFO  Execute project builders (done) | time=1ms
16:40:08.076 INFO  Project key: Ekart
16:40:08.076 INFO  Base dir: /var/lib/jenkins/workspace/CI_pipeline
16:40:08.076 INFO  Working dir: /var/lib/jenkins/workspace/CI_pipeline/.scannerwork
16:40:08.085 INFO  Load project settings for component key: 'Ekart'
16:40:08.109 INFO  Load project settings for component key: 'Ekart' (done) | time=24ms
16:40:08.278 INFO  Auto-configuring with CI 'Jenkins'
16:40:08.283 INFO  Load quality profiles
16:40:08.328 INFO  Load quality profiles (done) | time=45ms
16:40:08.332 INFO  Load active rules
16:40:09.419 INFO  Load active rules (done) | time=1087ms
16:40:09.423 INFO  Load analysis cache
16:40:09.433 INFO  Load analysis cache (705 bytes) | time=9ms
16:40:09.470 INFO  Load project repositories
16:40:09.485 INFO  Load project repositories (done) | time=15ms
16:40:09.521 INFO  Indexing files...
16:40:09.521 INFO  Project configuration:
16:40:09.739 INFO  45 files indexed
16:40:09.740 INFO  38 files ignored because of scm ignore settings
16:40:09.741 INFO  Quality profile for css: Sonar way
16:40:09.741 INFO  Quality profile for java: Sonar way
16:40:09.741 INFO  Quality profile for web: Sonar way
16:40:09.741 INFO  Quality profile for xml: Sonar way
16:40:09.741 INFO  Quality profile for yaml: Sonar way
16:40:09.741 INFO  ------------- Run sensors on module Ekart
16:40:09.829 INFO  Load metrics repository
16:40:09.858 INFO  Load metrics repository (done) | time=29ms
16:40:11.177 INFO  Sensor JavaSensor [java]
16:40:11.185 INFO  Configured Java source version (sonar.java.source): none
16:40:11.189 INFO  JavaClasspath initialization
16:40:11.192 INFO  JavaClasspath initialization (done) | time=3ms
16:40:11.192 INFO  JavaTestClasspath initialization
16:40:11.193 INFO  JavaTestClasspath initialization (done) | time=1ms
16:40:11.205 INFO  Server-side caching is enabled. The Java analyzer will not try to leverage data from a previous analysis.
16:40:11.208 INFO  Using ECJ batch to parse 24 Main java source files with batch size 50 KB.
16:40:11.405 INFO  Starting batch processing.
16:40:11.901 INFO  The Java analyzer cannot skip unchanged files in this context. A full analysis is performed for all files.
16:40:12.892 INFO  100% analyzed
16:40:12.892 INFO  Batch processing: Done.
16:40:12.893 INFO  Did not optimize analysis for any files, performed a full analysis for all 24 files.
16:40:12.894 WARN  Dependencies/libraries were not provided for analysis of SOURCE files. The 'sonar.java.libraries' property is empty. Verify your configuration, as you might end up with less precise results.
16:40:12.894 WARN  Unresolved imports/types have been detected during analysis. Enable DEBUG mode to see them.
16:40:12.895 WARN  Use of preview features have been detected during analysis. Enable DEBUG mode to see them.
16:40:12.895 INFO  No "Test" source files to scan.
16:40:12.895 INFO  No "Generated" source files to scan.
16:40:12.895 INFO  Sensor JavaSensor [java] (done) | time=1719ms
16:40:12.895 INFO  Sensor JaCoCo XML Report Importer [jacoco]
16:40:12.896 INFO  'sonar.coverage.jacoco.xmlReportPaths' is not defined. Using default locations: target/site/jacoco/jacoco.xml,target/site/jacoco-it/jacoco.xml,build/reports/jacoco/test/jacocoTestReport.xml
16:40:12.896 INFO  No report imported, no coverage information will be imported by JaCoCo XML Report Importer
16:40:12.896 INFO  Sensor JaCoCo XML Report Importer [jacoco] (done) | time=1ms
16:40:12.896 INFO  Sensor IaC CloudFormation Sensor [iac]
16:40:12.903 INFO  0 source files to be analyzed
16:40:12.909 INFO  0/0 source files have been analyzed
16:40:12.909 INFO  Sensor IaC CloudFormation Sensor [iac] (done) | time=13ms
16:40:12.909 INFO  Sensor IaC Kubernetes Sensor [iac]
16:40:12.911 INFO  1 source file to be analyzed
16:40:12.947 INFO  1/1 source file has been analyzed
16:40:12.948 INFO  Sensor IaC Kubernetes Sensor [iac] (done) | time=39ms
16:40:12.948 INFO  Sensor JavaScript inside YAML analysis [javascript]
16:40:12.951 INFO  No input files found for analysis
16:40:12.952 INFO  Hit the cache for 0 out of 0
16:40:12.953 INFO  Miss the cache for 0 out of 0
16:40:12.953 INFO  Sensor JavaScript inside YAML analysis [javascript] (done) | time=5ms
16:40:12.953 INFO  Sensor CSS Rules [javascript]
16:40:14.130 ERROR Error when running: 'node -v'. Is Node.js available during analysis?
org.sonarsource.nodejs.NodeCommandException: Error when running: 'node -v'. Is Node.js available during analysis?
	at org.sonarsource.nodejs.NodeCommand.start(NodeCommand.java:79)
	at org.sonarsource.nodejs.NodeCommandBuilderImpl.getVersion(NodeCommandBuilderImpl.java:203)
	at org.sonarsource.nodejs.NodeCommandBuilderImpl.checkNodeCompatibility(NodeCommandBuilderImpl.java:169)
	at org.sonarsource.nodejs.NodeCommandBuilderImpl.build(NodeCommandBuilderImpl.java:143)
	at org.sonar.plugins.javascript.eslint.EslintBridgeServerImpl.initNodeCommand(EslintBridgeServerImpl.java:201)
	at org.sonar.plugins.javascript.eslint.EslintBridgeServerImpl.startServer(EslintBridgeServerImpl.java:142)
	at org.sonar.plugins.javascript.eslint.EslintBridgeServerImpl.startServerLazily(EslintBridgeServerImpl.java:233)
	at org.sonar.plugins.javascript.eslint.AbstractEslintSensor.execute(AbstractEslintSensor.java:68)
	at org.sonar.plugins.javascript.eslint.CssRuleSensor.execute(CssRuleSensor.java:89)
	at org.sonar.scanner.sensor.AbstractSensorWrapper.analyse(AbstractSensorWrapper.java:64)
	at org.sonar.scanner.sensor.ModuleSensorsExecutor.execute(ModuleSensorsExecutor.java:88)
	at org.sonar.scanner.sensor.ModuleSensorsExecutor.lambda$execute$1(ModuleSensorsExecutor.java:61)
	at org.sonar.scanner.sensor.ModuleSensorsExecutor.withModuleStrategy(ModuleSensorsExecutor.java:79)
	at org.sonar.scanner.sensor.ModuleSensorsExecutor.execute(ModuleSensorsExecutor.java:61)
	at org.sonar.scanner.scan.SpringModuleScanContainer.doAfterStart(SpringModuleScanContainer.java:82)
	at org.sonar.core.platform.SpringComponentContainer.startComponents(SpringComponentContainer.java:188)
	at org.sonar.core.platform.SpringComponentContainer.execute(SpringComponentContainer.java:167)
	at org.sonar.scanner.scan.SpringProjectScanContainer.scan(SpringProjectScanContainer.java:403)
	at org.sonar.scanner.scan.SpringProjectScanContainer.scanRecursively(SpringProjectScanContainer.java:399)
	at org.sonar.scanner.scan.SpringProjectScanContainer.doAfterStart(SpringProjectScanContainer.java:368)
	at org.sonar.core.platform.SpringComponentContainer.startComponents(SpringComponentContainer.java:188)
	at org.sonar.core.platform.SpringComponentContainer.execute(SpringComponentContainer.java:167)
	at org.sonar.scanner.bootstrap.SpringGlobalContainer.doAfterStart(SpringGlobalContainer.java:137)
	at org.sonar.core.platform.SpringComponentContainer.startComponents(SpringComponentContainer.java:188)
	at org.sonar.core.platform.SpringComponentContainer.execute(SpringComponentContainer.java:167)
	at org.sonar.batch.bootstrapper.Batch.doExecute(Batch.java:72)
	at org.sonar.batch.bootstrapper.Batch.execute(Batch.java:66)
	at org.sonarsource.scanner.lib.internal.batch.BatchIsolatedLauncher.execute(BatchIsolatedLauncher.java:41)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:77)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:569)
	at org.sonarsource.scanner.lib.internal.facade.inprocess.IsolatedLauncherProxy.invoke(IsolatedLauncherProxy.java:62)
	at jdk.proxy3/jdk.proxy3.$Proxy2.execute(Unknown Source)
	at org.sonarsource.scanner.lib.internal.facade.inprocess.InProcessScannerEngineFacade.doAnalyze(InProcessScannerEngineFacade.java:38)
	at org.sonarsource.scanner.lib.internal.facade.AbstractScannerEngineFacade.analyze(AbstractScannerEngineFacade.java:65)
	at org.sonarsource.scanner.cli.Main.analyze(Main.java:79)
	at org.sonarsource.scanner.cli.Main.main(Main.java:64)
Caused by: java.io.IOException: Cannot run program "node": error=2, No such file or directory
	at java.base/java.lang.ProcessBuilder.start(ProcessBuilder.java:1143)
	at java.base/java.lang.ProcessBuilder.start(ProcessBuilder.java:1073)
	at org.sonarsource.nodejs.ProcessWrapperImpl.startProcess(ProcessWrapperImpl.java:39)
	at org.sonarsource.nodejs.NodeCommand.start(NodeCommand.java:77)
	... 37 common frames omitted
Caused by: java.io.IOException: error=2, No such file or directory
	at java.base/java.lang.ProcessImpl.forkAndExec(Native Method)
	at java.base/java.lang.ProcessImpl.<init>(ProcessImpl.java:314)
	at java.base/java.lang.ProcessImpl.start(ProcessImpl.java:244)
	at java.base/java.lang.ProcessBuilder.start(ProcessBuilder.java:1110)
	... 40 common frames omitted

16:40:14.131 INFO  Hit the cache for 0 out of 0
16:40:14.131 INFO  Miss the cache for 0 out of 0
16:40:14.131 INFO  Sensor CSS Rules [javascript] (done) | time=1178ms
16:40:14.131 INFO  Sensor CSS Metrics [javascript]
16:40:14.143 INFO  Sensor CSS Metrics [javascript] (done) | time=12ms
16:40:14.143 INFO  Sensor C# Project Type Information [csharp]
16:40:14.144 INFO  Sensor C# Project Type Information [csharp] (done) | time=1ms
16:40:14.144 INFO  Sensor C# Analysis Log [csharp]
16:40:14.172 INFO  Sensor C# Analysis Log [csharp] (done) | time=28ms
16:40:14.173 INFO  Sensor C# Properties [csharp]
16:40:14.173 INFO  Sensor C# Properties [csharp] (done) | time=0ms
16:40:14.173 INFO  Sensor SurefireSensor [java]
16:40:14.173 INFO  parsing [/var/lib/jenkins/workspace/CI_pipeline/target/surefire-reports]
16:40:14.173 INFO  Sensor SurefireSensor [java] (done) | time=0ms
16:40:14.174 INFO  Sensor HTML [web]
16:40:14.257 INFO  Sensor HTML [web] (done) | time=82ms
16:40:14.257 INFO  Sensor XML Sensor [xml]
16:40:14.262 INFO  1 source file to be analyzed
16:40:14.489 INFO  1/1 source file has been analyzed
16:40:14.489 INFO  Sensor XML Sensor [xml] (done) | time=232ms
16:40:14.489 INFO  Sensor TextAndSecretsSensor [text]
16:40:14.501 INFO  37 source files to be analyzed
16:40:14.552 INFO  37/37 source files have been analyzed
16:40:14.552 INFO  Sensor TextAndSecretsSensor [text] (done) | time=63ms
16:40:14.552 INFO  Sensor VB.NET Project Type Information [vbnet]
16:40:14.553 INFO  Sensor VB.NET Project Type Information [vbnet] (done) | time=1ms
16:40:14.553 INFO  Sensor VB.NET Analysis Log [vbnet]
16:40:14.583 INFO  Sensor VB.NET Analysis Log [vbnet] (done) | time=30ms
16:40:14.583 INFO  Sensor VB.NET Properties [vbnet]
16:40:14.583 INFO  Sensor VB.NET Properties [vbnet] (done) | time=0ms
16:40:14.583 INFO  Sensor IaC Docker Sensor [iac]
16:40:14.585 INFO  1 source file to be analyzed
16:40:14.645 INFO  1/1 source file has been analyzed
16:40:14.645 INFO  Sensor IaC Docker Sensor [iac] (done) | time=62ms
16:40:14.650 INFO  ------------- Run sensors on project
16:40:14.714 INFO  Sensor Analysis Warnings import [csharp]
16:40:14.715 INFO  Sensor Analysis Warnings import [csharp] (done) | time=1ms
16:40:14.715 INFO  Sensor Zero Coverage Sensor
16:40:14.725 INFO  Sensor Zero Coverage Sensor (done) | time=10ms
16:40:14.727 INFO  Sensor Java CPD Block Indexer
16:40:14.755 INFO  Sensor Java CPD Block Indexer (done) | time=27ms
16:40:14.770 INFO  CPD Executor 11 files had no CPD blocks
16:40:14.770 INFO  CPD Executor Calculating CPD for 23 files
16:40:14.785 INFO  CPD Executor CPD calculation finished (done) | time=14ms
16:40:14.865 INFO  Analysis report generated in 74ms, dir size=222.4 kB
16:40:14.905 INFO  Analysis report compressed in 39ms, zip size=93.8 kB
16:40:14.975 INFO  Analysis report uploaded in 68ms
16:40:14.976 INFO  ANALYSIS SUCCESSFUL, you can find the results at: http://13.200.200.247:9000/dashboard?id=Ekart
16:40:14.977 INFO  Note that you will be able to access the updated dashboard once the server has processed the submitted analysis report
16:40:14.977 INFO  More about the report processing at http://13.200.200.247:9000/api/ce/task?id=AZpPvZFGkmtuuJ4OnvZp
16:40:15.232 INFO  Analysis total time: 7.598 s
16:40:15.233 INFO  EXECUTION SUCCESS
16:40:15.234 INFO  Total time: 9.384s
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Build Application)
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] withEnv
[Pipeline] {
[Pipeline] sh
+ mvn clean install -DskipTests=true
[INFO] Scanning for projects...
[INFO] 
[INFO] ---------------------< com.reljicd:shopping-cart >----------------------
[INFO] Building shopping-cart 0.0.1-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- clean:2.6.1:clean (default-clean) @ shopping-cart ---
[INFO] Deleting /var/lib/jenkins/workspace/CI_pipeline/target
[INFO] 
[INFO] --- jacoco:0.8.6:prepare-agent (jacoco-initialize) @ shopping-cart ---
[INFO] argLine set to -javaagent:/var/lib/jenkins/.m2/repository/org/jacoco/org.jacoco.agent/0.8.6/org.jacoco.agent-0.8.6-runtime.jar=destfile=/var/lib/jenkins/workspace/CI_pipeline/target/jacoco.exec
[INFO] 
[INFO] --- resources:2.6:resources (default-resources) @ shopping-cart ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO] Copying 12 resources
[INFO] 
[INFO] --- compiler:3.1:compile (default-compile) @ shopping-cart ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 23 source files to /var/lib/jenkins/workspace/CI_pipeline/target/classes
[INFO] 
[INFO] --- resources:2.6:testResources (default-testResources) @ shopping-cart ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /var/lib/jenkins/workspace/CI_pipeline/src/test/resources
[INFO] 
[INFO] --- compiler:3.1:testCompile (default-testCompile) @ shopping-cart ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to /var/lib/jenkins/workspace/CI_pipeline/target/test-classes
[INFO] 
[INFO] --- surefire:2.18.1:test (default-test) @ shopping-cart ---
[INFO] Tests are skipped.
[INFO] 
[INFO] --- jar:2.6:jar (default-jar) @ shopping-cart ---
[INFO] Building jar: /var/lib/jenkins/workspace/CI_pipeline/target/shopping-cart-0.0.1-SNAPSHOT.jar
[INFO] 
[INFO] --- spring-boot:1.5.3.RELEASE:repackage (default) @ shopping-cart ---
[INFO] 
[INFO] --- jacoco:0.8.6:report (jacoco-site) @ shopping-cart ---
[INFO] Skipping JaCoCo execution due to missing execution data file.
[INFO] 
[INFO] --- install:2.5.2:install (default-install) @ shopping-cart ---
[INFO] Installing /var/lib/jenkins/workspace/CI_pipeline/target/shopping-cart-0.0.1-SNAPSHOT.jar to /var/lib/jenkins/.m2/repository/com/reljicd/shopping-cart/0.0.1-SNAPSHOT/shopping-cart-0.0.1-SNAPSHOT.jar
[INFO] Installing /var/lib/jenkins/workspace/CI_pipeline/pom.xml to /var/lib/jenkins/.m2/repository/com/reljicd/shopping-cart/0.0.1-SNAPSHOT/shopping-cart-0.0.1-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  5.472 s
[INFO] Finished at: 2025-11-04T16:40:23Z
[INFO] ------------------------------------------------------------------------
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Build & Push Docker Image)
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] withEnv
[Pipeline] {
[Pipeline] script
[Pipeline] {
[Pipeline] withDockerRegistry
$ /var/lib/jenkins/tools/org.jenkinsci.plugins.docker.commons.tools.DockerTool/docker/bin/docker login -u nitin241 -p ******** https://index.docker.io/v1/
Login Succeeded
[Pipeline] {
[Pipeline] sh
+ docker build -t ekart:latest -f docker/Dockerfile .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  48.42MB

Step 1/6 : FROM openjdk:8u151-jdk-alpine3.7
 ---> cc2179b8f042
Step 2/6 : EXPOSE 8070
 ---> Using cache
 ---> b259b55d7355
Step 3/6 : ENV APP_HOME /usr/src/app
 ---> Using cache
 ---> ab38d93570ae
Step 4/6 : COPY target/shopping-cart-0.0.1-SNAPSHOT.jar $APP_HOME/app.jar
 ---> 9fbe1cbd89bd
Step 5/6 : WORKDIR $APP_HOME
 ---> Running in ad6225a561af
 ---> Removed intermediate container ad6225a561af
 ---> 9409bdf94978
Step 6/6 : ENTRYPOINT exec java -jar app.jar
 ---> Running in 1825bd6dd348
 ---> Removed intermediate container 1825bd6dd348
 ---> 7cb926158702
Successfully built 7cb926158702
Successfully tagged ekart:latest
[Pipeline] sh
+ docker tag ekart:latest nitin241/ekart:latest
[Pipeline] sh
+ docker push nitin241/ekart:latest
The push refers to repository [docker.io/nitin241/ekart]
21f3b617ed0b: Preparing
01bca7cb4826: Preparing
a8cc3712c14a: Preparing
cd7100a72410: Preparing
cd7100a72410: Layer already exists
a8cc3712c14a: Layer already exists
01bca7cb4826: Layer already exists
21f3b617ed0b: Pushed
latest: digest: sha256:4e0739343093a8797502dc868104e1018f5854d73c9e4644bea7466b68f1c32d size: 1159
[Pipeline] }
[Pipeline] // withDockerRegistry
[Pipeline] }
[Pipeline] // script
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Trigger CD Pipeline)
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] withEnv
[Pipeline] {
[Pipeline] build
Scheduling project: CD_PIPELEINE
Starting building: CD_PIPELEINE #2
Build CD_PIPELEINE #2 completed: SUCCESS
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS
[#34.txt](https://github.com/user-attachments/files/23341657/34.txt)
ut



<img width="1920" height="1020" alt="Screenshot 2025-11-04 221539" src="https://github.com/user-attachments/assets/3513be73-25aa-4adc-b0c2-0bafa6e37566" />



<img width="1920" height="1020" alt="Screenshot 2025-11-04 221606" src="https://github.com/user-attachments/assets/1f7b7797-bc86-44e2-bad9-4afa76d278c8" />

This is a demo project for practicing Spring + Thymeleaf. The idea was to build some basic shopping cart web app.

It was made using **Spring Boot**, **Spring Security**, **Thymeleaf**, **Spring Data JPA**, **Spring Data REST and Docker**. 
Database is in memory **H2**.

There is a login and registration functionality included.

Users can shop for products. Each user has his own shopping cart (session functionality).
Checkout is transactional.

## Configuration

### Configuration Files

Folder **src/resources/** contains config files for **shopping-cart** Spring Boot application.

* **src/resources/application.properties** - main configuration file. Here it is possible to change admin username/password,
as well as change the port number.

## How to run

There are several ways to run the application. You can run it from the command line with included Maven Wrapper, Maven or Docker. 

Once the app starts, go to the web browser and visit `http://localhost:8070/home`

Admin username: **admin**

Admin password: **admin**

User username: **user**

User password: **password**

### Maven Wrapper

#### Using the Maven Plugin

Go to the root folder of the application and type:
```bash
$ chmod +x scripts/mvnw
$ scripts/mvnw spring-boot:run
```

#### Using Executable Jar

Or you can build the JAR file with 
```bash
$ scripts/mvnw clean package
``` 

Then you can run the JAR file:
```bash
$ java -jar target/shopping-cart-0.0.1-SNAPSHOT.jar
```

### Maven

Open a terminal and run the following commands to ensure that you have valid versions of Java and Maven installed:

```bash
$ java -version
java version "1.8.0_102"
Java(TM) SE Runtime Environment (build 1.8.0_102-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.102-b14, mixed mode)
```

```bash
$ mvn -v
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-10T16:41:47+00:00)
Maven home: /usr/local/Cellar/maven/3.3.9/libexec
Java version: 1.8.0_102, vendor: Oracle Corporation
```

#### Using the Maven Plugin

The Spring Boot Maven plugin includes a run goal that can be used to quickly compile and run your application. 
Applications run in an exploded form, as they do in your IDE. 
The following example shows a typical Maven command to run a Spring Boot application:
 
```bash
$ mvn spring-boot:run
``` 

#### Using Executable Jar

To create an executable jar run:

```bash
$ mvn clean package
``` 

To run that application, use the java -jar command, as follows:

```bash
$ java -jar target/shopping-cart-0.0.1-SNAPSHOT.jar
```

To exit the application, press **ctrl-c**.

### Docker

It is possible to run **shopping-cart** using Docker:

Build Docker image:
```bash
$ mvn clean package
$ docker build -t shopping-cart:dev -f docker/Dockerfile .
```

Run Docker container:
```bash
$ docker run --rm -i -p 8070:8070 \
      --name shopping-cart \
      shopping-cart:dev
```

##### Helper script

It is possible to run all of the above with helper script:

```bash
$ chmod +x scripts/run_docker.sh
$ scripts/run_docker.sh
```

## Docker 

Folder **docker** contains:

* **docker/shopping-cart/Dockerfile** - Docker build file for executing shopping-cart Docker image. 
Instructions to build artifacts, copy build artifacts to docker image and then run app on proper port with proper configuration file.

## Util Scripts

* **scripts/run_docker.sh.sh** - util script for running shopping-cart Docker container using **docker/Dockerfile**

## Tests

Tests can be run by executing following command from the root of the project:

```bash
$ mvn test
```

## Helper Tools

### HAL REST Browser

Go to the web browser and visit `http://localhost:8070/`

You will need to be authenticated to be able to see this page.

### H2 Database web interface

Go to the web browser and visit `http://localhost:8070/h2-console`

In field **JDBC URL** put 
```
jdbc:h2:mem:shopping_cart_db
```

In `/src/main/resources/application.properties` file it is possible to change both
web interface url path, as well as the datasource url.
