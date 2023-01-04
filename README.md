# Reproducer Project for Quarkus Issues 30101

This project is a reproducer for [Quarkus Issue 30101](https://github.com/quarkusio/quarkus/issues/30101).

## Initial execution

```shell script
$ ./gradlew -Dquarkus.package.type=native build testNative
...
Status: Downloaded newer image for quay.io/quarkus/ubi-quarkus-mandrel-builder-image:22.3-java17
quay.io/quarkus/ubi-quarkus-mandrel-builder-image:22.3-java17
========================================================================================================================
GraalVM Native Image: Generating 'quarkus-issues-30101-1.0.0-SNAPSHOT-runner' (executable)...
========================================================================================================================
[1/7] Initializing...                                                                                    (6.1s @ 0.23GB)
 Version info: 'GraalVM 22.3.0.1-Final Java 17 Mandrel Distribution'
 Java version info: '17.0.5+8'
 C compiler: gcc (redhat, x86_64, 8.5.0)
 Garbage collector: Serial GC
 3 user-specific feature(s)
 - io.quarkus.runner.Feature: Auto-generated class by Quarkus from the existing extensions
 - io.quarkus.runtime.graal.DisableLoggingFeature: Disables INFO logging during the analysis phase for the [org.jboss.threads] categories
 - io.quarkus.runtime.graal.ResourcesFeature: Register each line in META-INF/quarkus-native-resources.txt as a resource on Substrate VM
[2/7] Performing analysis...  [******]                                                                  (37.3s @ 3.15GB)
  10,202 (88.30%) of 11,554 classes reachable
  14,501 (57.21%) of 25,346 fields reachable
  50,248 (55.22%) of 90,988 methods reachable
     507 classes,   111 fields, and 2,354 methods registered for reflection
      63 classes,    68 fields, and    55 methods registered for JNI access
       4 native libraries: dl, pthread, rt, z
[3/7] Building universe...                                                                               (6.2s @ 1.30GB)
[4/7] Parsing methods...      [**]                                                                       (3.2s @ 4.57GB)
[5/7] Inlining methods...     [***]                                                                      (1.6s @ 2.04GB)
[6/7] Compiling methods...    [*****]                                                                   (22.6s @ 2.17GB)
[7/7] Creating image...                                                                                  (4.3s @ 3.80GB)
  18.80MB (47.46%) for code area:    32,106 compilation units
  20.49MB (51.74%) for image heap:  265,785 objects and 10 resources
 327.40KB ( 0.81%) for other data
  39.61MB in total
------------------------------------------------------------------------------------------------------------------------
Top 10 packages in code area:                               Top 10 object types in image heap:
   1.62MB sun.security.ssl                                     4.18MB byte[] for code metadata
 963.28KB java.util                                            2.40MB java.lang.Class
 730.07KB java.lang.invoke                                     2.40MB java.lang.String
 718.53KB com.sun.crypto.provider                              2.29MB byte[] for general heap data
 450.53KB sun.security.x509                                    1.88MB byte[] for java.lang.String
 443.13KB java.lang                                          876.73KB com.oracle.svm.core.hub.DynamicHubCompanion
 393.49KB io.netty.buffer                                    628.97KB java.util.HashMap$Node
 384.36KB java.io                                            533.40KB byte[] for reflection metadata
 381.54KB java.util.concurrent                               462.24KB java.lang.String[]
 342.22KB io.netty.handler.codec.http2                       393.57KB byte[] for embedded resources
  12.24MB for 379 more packages                                4.25MB for 2501 more object types
------------------------------------------------------------------------------------------------------------------------
                        2.6s (3.0% of total time) in 28 GCs | Peak RSS: 5.97GB | CPU load: 8.06
------------------------------------------------------------------------------------------------------------------------
Produced artifacts:
 /project/quarkus-issues-30101-1.0.0-SNAPSHOT-runner (executable)
 /project/quarkus-issues-30101-1.0.0-SNAPSHOT-runner-build-output-stats.json (json)
 /project/quarkus-issues-30101-1.0.0-SNAPSHOT-runner-timing-stats.json (raw)
 /project/quarkus-issues-30101-1.0.0-SNAPSHOT-runner.build_artifacts.txt (txt)
========================================================================================================================
Finished generating 'quarkus-issues-30101-1.0.0-SNAPSHOT-runner' in 1m 26s.

BUILD SUCCESSFUL in 2m 43s
11 actionable tasks: 3 executed, 8 up-to-date
```

Test results:

```shell
$ cat build/test-results/testNative/TEST-org.acme.GreetingResourceIT.xml                                                                                                main 
<?xml version="1.0" encoding="UTF-8"?>
<testsuite name="org.acme.GreetingResourceIT" tests="1" skipped="0" failures="0" errors="0" timestamp="2023-01-03T11:03:14" hostname="ws13.trikorasolutions.net" time="1.246">
  <properties/>
  <testcase name="testHelloEndpoint()" classname="org.acme.GreetingResourceIT" time="1.246"/>
  <system-out><![CDATA[Executing "/my-project-home/quarkus-issues-30101/build/quarkus-issues-30101-1.0.0-SNAPSHOT-runner -Dquarkus.http.port=8081 -Dquarkus.http.ssl-port=8444 -Dtest.url=http://localhost:8081 -Dquarkus.log.file.path=/my-project-home/quarkus-issues-30101/build/quarkus.log -Dquarkus.log.file.enable=true"
__  ____  __  _____   ___  __ ____  ______ 
 --/ __ \/ / / / _ | / _ \/ //_/ / / / __/ 
 -/ /_/ / /_/ / __ |/ , _/ ,< / /_/ /\ \   
--\___\_\____/_/ |_/_/|_/_/|_|\____/___/   
2023-01-03 12:03:14,432 INFO  [io.quarkus] (main) quarkus-issues-30101 1.0.0-SNAPSHOT native (powered by Quarkus 2.15.1.Final) started in 0.019s. Listening on: http://0.0.0.0:8081
2023-01-03 12:03:14,432 INFO  [io.quarkus] (main) Profile prod activated. 
2023-01-03 12:03:14,432 INFO  [io.quarkus] (main) Installed features: [cdi, resteasy-reactive, smallrye-context-propagation, vertx]
]]></system-out>
  <system-err><![CDATA[]]></system-err>
</testsuite>

```

## Error reproduction

Cleanup the project.

```shell
$ ./gradlew clean

BUILD SUCCESSFUL in 864ms
1 actionable task: 1 executed
```

```shell
$ ./gradlew -Dquarkus.package.type=native -Dquarkus.package.output-name=test -Dquarkus.package.add-runner-suffix=false build testNative
...
Finished generating 'test' in 1m 31s.

> Task :testNative FAILED

GreetingResourceIT > testHelloEndpoint() FAILED
    java.lang.RuntimeException at QuarkusIntegrationTestExtension.java:337
        Caused by: java.io.IOException at ProcessBuilder.java:1143
            Caused by: java.io.IOException at ProcessImpl.java:-2

1 test completed, 1 failed

FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':testNative'.
> There were failing tests. See the report at: file:///my-project-home/quarkus-issues-30101/build/reports/tests/testNative/index.html
```

The error on the report was: 

```
java.lang.RuntimeException: java.io.IOException: Cannot run program "/my-project-home/quarkus-issues-30101/build/quarkus-issues-30101-1.0.0-SNAPSHOT-runner": error=2, No such file or directory`
...
Caused by: java.io.IOException: Cannot run program "/my-project-home/quarkus-issues-30101/build/quarkus-issues-30101-1.0.0-SNAPSHOT-runner": error=2, No such file or directory
...
Caused by: java.io.IOException: error=2, No such file or directory
...
```

## After fix

Install the snapshot version containing the fix on `maven local`.

```shell
$ ./mvnw install -T0.6C  -Dno-test-modules -DskipTests -DskipITs
```

Update this example to use the `999-SNAPSHOT` quarkus version which has been installed locally.

Relaunch the tests.

```shell
$ ./gradlew -Dquarkus.package.type=native -Dquarkus.package.output-name=test -Dquarkus.package.add-runner-suffix=false build testNative

...
========================================================================================================================
GraalVM Native Image: Generating 'test' (executable)...
========================================================================================================================
...
Finished generating 'test' in 1m 41s.

BUILD SUCCESSFUL in 2m 5s
12 actionable tasks: 9 executed, 3 from cache

````


```shell
$ ./gradlew -Dquarkus.package.type=native -Dquarkus.package.output-name=test clean build testNative

...
========================================================================================================================
GraalVM Native Image: Generating 'test-runner' (executable)...
========================================================================================================================
...
Finished generating 'test-runner' in 1m 45s.

BUILD SUCCESSFUL in 2m 13s
12 actionable tasks: 7 executed, 4 from cache, 1 up-to-date
````


```shell
$ ./gradlew -Dquarkus.package.type=native clean build testNative

...
========================================================================================================================
GraalVM Native Image: Generating 'quarkus-issue-30101-1.0.0-SNAPSHOT-runner' (executable)...
========================================================================================================================
...
Finished generating 'quarkus-issue-30101-1.0.0-SNAPSHOT-runner' in 2m 1s.

BUILD SUCCESSFUL in 2m 16s
12 actionable tasks: 6 executed, 5 from cache, 1 up-to-date
````

## Quarkus
This project uses Quarkus, the Supersonic Subatomic Java Framework.

If you want to learn more about Quarkus, please visit its website: https://quarkus.io/ .

### Running the application in dev mode

You can run your application in dev mode that enables live coding using:
```shell script
./gradlew quarkusDev
```

> **_NOTE:_**  Quarkus now ships with a Dev UI, which is available in dev mode only at http://localhost:8080/q/dev/.

### Packaging and running the application

The application can be packaged using:
```shell script
./gradlew build
```
It produces the `quarkus-run.jar` file in the `build/quarkus-app/` directory.
Be aware that it’s not an _über-jar_ as the dependencies are copied into the `build/quarkus-app/lib/` directory.

The application is now runnable using `java -jar build/quarkus-app/quarkus-run.jar`.

If you want to build an _über-jar_, execute the following command:
```shell script
./gradlew build -Dquarkus.package.type=uber-jar
```

The application, packaged as an _über-jar_, is now runnable using `java -jar build/*-runner.jar`.

### Creating a native executable

You can create a native executable using: 
```shell script
./gradlew build -Dquarkus.package.type=native
```

Or, if you don't have GraalVM installed, you can run the native executable build in a container using: 
```shell script
./gradlew build -Dquarkus.package.type=native -Dquarkus.native.container-build=true
```

You can then execute your native executable with: `./build/quarkus-issues-30101-1.0.0-SNAPSHOT-runner`

If you want to learn more about building native executables, please consult https://quarkus.io/guides/gradle-tooling.

### Provided Code

#### RESTEasy Reactive

Easily start your Reactive RESTful Web Services

[Related guide section...](https://quarkus.io/guides/getting-started-reactive#reactive-jax-rs-resources)
