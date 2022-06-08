# Simple Application for Java Gradle

Follow [Building Java Libraries Sample with Gradle](https://docs.gradle.org/current/samples/sample_building_java_libraries.html) guide to create this project.

``` bash

$ gradle init

Welcome to Gradle 7.4.2!

Here are the highlights of this release:
 - Aggregated test and JaCoCo reports
 - Marking additional test source directories as tests in IntelliJ
 - Support for Adoptium JDKs in Java toolchains

For more details see https://docs.gradle.org/7.4.2/release-notes.html

Starting a Gradle Daemon (subsequent builds will be faster)

Select type of project to generate:
  1: basic
  2: application
  3: library
  4: Gradle plugin
Enter selection (default: basic) [1..4] 3

Select implementation language:
  1: C++
  2: Groovy
  3: Java
  4: Kotlin
  5: Scala
  6: Swift
Enter selection (default: Java) [1..6] 3

Select build script DSL:
  1: Groovy
  2: Kotlin
Enter selection (default: Groovy) [1..2] 1

Generate build using new APIs and behavior (some features may change in the next minor release)? (default: no) [yes, no] yes
Select test framework:
  1: JUnit 4
  2: TestNG
  3: Spock
  4: JUnit Jupiter
Enter selection (default: JUnit Jupiter) [1..4] 1

Project name (default: simple-java-gradle-app):
Source package (default: simple.java.gradle.app):

> Task :init
Get more help with your project: https://docs.gradle.org/7.4.2/samples/sample_building_java_libraries.html

BUILD SUCCESSFUL in 42s
2 actionable tasks: 2 executed
The operation couldn’t be completed. Unable to locate a Java Runtime.
Please visit http://www.java.com for information on installing Java.

```

## Example for integrating with Nexus Maven Repository

1. Start Nexus repository, the below script starts an Nexus3 application container.

> sealio/sonatype-nexus3 works on ARM64 arch laptop.

``` bash

$ docker run -d -p 8081:8081 sealio/sonatype-nexus3:3.38.0

```

1. Needing configure after launching Nexus3 at first time.

    - Reconfiguring the password of Administrator, for example `admin123`.

    - Allowing anonymous user accessing.

1. Modify `lib/build.gradle` as below.

``` groovy

plugins {
    ...

    // Apply the maven-publish plugin for release.
    id 'maven-publish'
}

group = rootProject.group
version = rootProject.version

repositories {
    maven {
        name = "maven-public"
        url  = "http://localhost:8081/repository/maven-public/"
        allowInsecureProtocol = true
    }
    mavenLocal()
}

...

publishing {
    publications {
        library(MavenPublication) {
            artifactId = rootProject.name

            from components.java

            pom {
                name = 'Simple Application for Java Gradle'
                description = 'An example project for Javar Gradle project'
                licenses {
                    license {
                        name = 'The Apache License, Version 2.0'
                        url = 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    }
                }
            }
        }
    }

    repositories {
        maven {
            def releasesRepoUrl = "http://localhost:8081/repository/maven-releases/"
            def snapshotsRepoUrl = "http://localhost:8081/repository/maven-snapshots/"

            name = "maven-publish"
            url = project.version.endsWith('SNAPSHOT') ? snapshotsRepoUrl : releasesRepoUrl
            allowInsecureProtocol = true
            credentials {
                username = "admin"
                password = "admin123"
            }
        }
    }
}

```

1. Try `./gradlew --no-deamon build` and `./gradlew --no-deamon publish`

## License

Copyright (c) 2022-present [Seal, Inc.](http://seal.io)

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the
License. You may obtain a copy of the License at

[http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "
AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific
language governing permissions and limitations under the License.
