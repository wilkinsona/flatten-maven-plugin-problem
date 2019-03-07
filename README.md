# Flatten Maven Plugin Problem with Maven 3.6.0

When producing a flattened pom with Maven 3.6.0, the Flatten Maven
Plugin is unable to resolve dependencies from a milestone repository
configured in the root pom. The problem does not occur with Maven
3.5.4.

## Reproduction

The problem can be reproduced by running the following command:

```
./mvnw clean process-resources
```

It should produce output similar to the following:

```
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO]
[INFO] parent                                                             [pom]
[INFO] child                                                              [pom]
[INFO]
[INFO] -------------------------< com.example:parent >-------------------------
[INFO] Building parent 1.0.0-SNAPSHOT                                     [1/2]
[INFO] --------------------------------[ pom ]---------------------------------
[INFO]
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ parent ---
[INFO]
[INFO] -------------------------< com.example:child >--------------------------
[INFO] Building child 1.0.0-SNAPSHOT                                      [2/2]
[INFO] --------------------------------[ pom ]---------------------------------
[INFO]
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ child ---
[INFO]
[INFO] --- flatten-maven-plugin:1.1.0:clean (flatten-clean) @ child ---
[INFO]
[INFO] --- flatten-maven-plugin:1.1.0:flatten (flatten) @ child ---
[INFO] Generating flattened POM of project com.example:child:pom:1.0.0-SNAPSHOT...
[WARNING] The POM for org.springframework.data.build:spring-data-build:pom:2.2.0.M2 is missing, no dependency information available
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary for parent 1.0.0-SNAPSHOT:
[INFO]
[INFO] parent ............................................. SUCCESS [  0.103 s]
[INFO] child .............................................. FAILURE [  0.392 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.592 s
[INFO] Finished at: 2019-03-07T21:21:12Z
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.codehaus.mojo:flatten-maven-plugin:1.1.0:flatten (flatten) on project child: 1 problem was encountered while building the effective model for com.example:child:1.0.0-SNAPSHOT
[ERROR] [FATAL] Non-resolvable parent POM for org.springframework.data:spring-data-releasetrain:Moore-M2: org.eclipse.aether.resolution.DependencyResolutionException: Failure to find org.springframework.data.build:spring-data-build:pom:2.2.0.M2 in https://repo.maven.apache.org/maven2 was cached in the local repository, resolution will not be reattempted until the update interval of central has elapsed or updates are forced @ org.springframework.data:spring-data-releasetrain:Moore-M2
[ERROR]
[ERROR] -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
[ERROR]
[ERROR] After correcting the problems, you can resume the build with the command
[ERROR]   mvn <goals> -rf :child
```

The problem does not occur with Maven 3.5.4:

```
./mvnw -N io.takari:maven:wrapper -Dmaven=3.5.4
[INFO] Scanning for projects...
[INFO]
[INFO] -------------------------< com.example:parent >-------------------------
[INFO] Building parent 1.0.0-SNAPSHOT
[INFO] --------------------------------[ pom ]---------------------------------
[INFO]
[INFO] --- maven:0.6.1:wrapper (default-cli) @ parent ---
[INFO]
[INFO] The Maven Wrapper version 0.4.2 has been successfully setup for your project.
[INFO] Using Apache Maven 3.5.4
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 0.340 s
[INFO] Finished at: 2019-03-07T21:22:54Z
[INFO] ------------------------------------------------------------------------
```

```
./mvnw clean process-resources
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO]
[INFO] parent                                                             [pom]
[INFO] child                                                              [pom]
[INFO]
[INFO] -------------------------< com.example:parent >-------------------------
[INFO] Building parent 1.0.0-SNAPSHOT                                     [1/2]
[INFO] --------------------------------[ pom ]---------------------------------
[INFO]
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ parent ---
[INFO]
[INFO] -------------------------< com.example:child >--------------------------
[INFO] Building child 1.0.0-SNAPSHOT                                      [2/2]
[INFO] --------------------------------[ pom ]---------------------------------
[INFO]
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ child ---
[INFO]
[INFO] --- flatten-maven-plugin:1.1.0:clean (flatten-clean) @ child ---
[INFO]
[INFO] --- flatten-maven-plugin:1.1.0:flatten (flatten) @ child ---
[INFO] Generating flattened POM of project com.example:child:pom:1.0.0-SNAPSHOT...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary:
[INFO]
[INFO] parent 1.0.0-SNAPSHOT .............................. SUCCESS [  0.099 s]
[INFO] child 1.0.0-SNAPSHOT ............................... SUCCESS [  0.328 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 0.533 s
[INFO] Finished at: 2019-03-07T21:23:22Z
[INFO] ------------------------------------------------------------------------
```