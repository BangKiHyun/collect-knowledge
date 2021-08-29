

# Gradle 멀티모듈 세팅 방법

Gradle을 사용한 멀티모듈 세팅 방법을 알아보자.

모듈은 다음과 같이 분리했다.

## core

- 시스템의 핵심 도메인 기능을 제공하는 모듈
-  Repository와 밀접한 중심 도메인을 다루는 계층
- 서비스에 대한 로직을 모름.

## common

- 각 모듈의 공통된 기능을 제공하는 모듈
- common모듈은 다른 모듈에 대한 의존성이 없어야 한다.
- ex) common exception, utill class 

## web

- api, service 관련 기능을 제공하는 모듈

## event

- event 처리 모듈

 </br >

자 그럼 이제 모듈을 생성해주자.

모듈을 생성하려면 기존 프로젝트에서 New -> Module을 선택해서 생성하면 된다.

![img](https://blog.kakaocdn.net/dn/pvl3Q/btrdrna2p40/9dBbX3o2DtKK6VPkS6kbP1/img.png)

이후 Gradle 선택 -> Java -> 모듈명을 입력하면 끝난다.

![img](https://blog.kakaocdn.net/dn/r593f/btrdo4DuzrR/9ML77Hvwdkhks7pYrkwA01/img.png)

![img](https://blog.kakaocdn.net/dn/M0kPV/btrdqiH1d9Z/1xq7nkIvh8KATNkrM8fA1K/img.png)

모듈을 만들었으면 다음과 같이 src, build.gradle 파일만 남기고 모두 삭제하면 된다.

![img](https://blog.kakaocdn.net/dn/zvgoB/btrdopOUGhM/Oh4Wkxa1waX2DdxLYZ755k/img.png)

자 그리고 settings.gradle을 수정해주자!

```java
rootProject.name = 'matcherloper'
include 'matcherloper-core'
include 'matcherloper-common'
include 'matcherloper-web'
include 'matcherloper-event'
```

위 코드는 matcherloper 프로젝트가 아래 4개의 프로젝트를 하위 프로젝트로 관리하겠다는 의미이다.

설정해 주지 않으면 해당 프로젝트를 찾을 수 없다는 오류가 발생한다.

</br > 

이제 root 프로젝트에 위치한 build.gradle을 수정해주자!

필자는 다음과 같이 설정했다.

```java
plugins {
    id 'org.springframework.boot' version '2.3.4.RELEASE'
    id 'io.spring.dependency-management' version '1.0.9.RELEASE'
    id "com.ewerk.gradle.plugins.querydsl" version "1.0.10"
}

def javaProjects = [project(":matcherloper-common"), project(":matcherloper-core"),
                    project(":matcherloper-web"), project(":matcherloper-event")]

configure(javaProjects) {
    apply plugin: "java"
    apply plugin: 'org.springframework.boot'
    apply plugin: 'io.spring.dependency-management'

    group = 'com.toy'
    version = '0.0.1-SNAPSHOT'

    sourceCompatibility = '1.8'

    repositories {
        mavenCentral()
    }

    dependencies {
        compileOnly 'org.projectlombok:lombok'
        annotationProcessor 'org.projectlombok:lombok'

        testImplementation 'org.junit.jupiter:junit-jupiter-api:5.3.1'
        testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.3.1'
    }

    test {
        useJUnitPlatform()
    }
}

def springProjects = [project(":matcherloper-core"), project(":matcherloper-web"),
                      project(":matcherloper-event")]

configure(springProjects) {

    repositories {
        mavenCentral()
    }

    dependencies {
        implementation 'org.springframework.boot:spring-boot-starter-web'
        testImplementation 'org.springframework.boot:spring-boot-starter-test'
    }
}

def queryDslProjects = [project(":matcherloper-core"), project(":matcherloper-web")]

configure(queryDslProjects) {
    apply plugin: "com.ewerk.gradle.plugins.querydsl"

    dependencies {
        implementation 'com.querydsl:querydsl-jpa'
    }

    def querydslDir = "$buildDir/generated/querydsl"

    querydsl {
        jpa = true
        querydslSourcesDir = querydslDir
    }
    sourceSets {
        main.java.srcDir querydslDir
    }
    configurations {
        querydsl.extendsFrom compileClasspath
    }
    compileQuerydsl {
        options.annotationProcessorPath = configurations.querydsl
    }
}
```

## 설명

코드를 보면 javaProjects, springProjects, queryDslProjects 가 보인다. 

- javaProjects에는 java 관련 의존성을 설정해주고, java가 필요한 모듈을 넣어줬다.
- springProjects에는 spring 관련 의존성을 설정해주고, spring이 필요한 모듈을 넣어줬다.
- queryDslProjects에는 queryDsl 관련 의존성을 설정해주고, queryDsl이 필요한 모듈을 넣어줬다.

참고로 Spring Boot와 Gradle 버전이 호환이 되지 않으면 특정 dependency를 인식하지 못할 수 있다.

</br >

필자는 기존에 Spring Boot는 2.3.4, Gradle은 7.0.2 버전을 사용했다. 그런데 testCompile group: 'org.assertj', name: 'assertj-core', version: '3.6.1' dependency를 인식하지 못했다.

Gradle 버전을 6.3으로 바꾸니 잘 돌아갔다. Spring Boot와 Gradle 버전을 맞춰 호환되게 만들자!

위와 같이 공통된 설정을 root 프로젝트의 build.gradle에서 설정해 줬다.</br >그리고 필요에 따라 각각의 모듈에서 build.gradle을 설정해 주면 된다.

</br >

## core 모듈 build.gradle

core 모듈에 있는 build.gradle을 다음과 같이 설정해줬다.

```java
bootJar { enabled = false }
jar { enabled = true }

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-jdbc'
    runtimeOnly 'com.h2database:h2'
}
```

core 모듈에 필요한 dependency를 추가해 줬다.

### bootJar.enable=false, jar.enable=true

IntelliJ 기본 테스트 러너에서는 IntelliJ에서 컴파일한 build 디렉터리를 기반으로 테스트를 진행해서 별다른 문제가 없다.

하지만 gradle 빌드 테스트의 경우에는 컴파일 및 빌드를 하면서 테스트가 진행되는데 위에서 언급한 **bootJar.enabled=false**만 선언된 모듈은 jar 파일을 생성하지 않기 때문에 이를 참조하는 하위 모듈에서 관련된 파일을 읽어오지 못하게 된다. 

그렇기 때문에 jar 파일을 만들기 위해 **jar.enable=true**를 설정해 줘야 한다. 

참고로 SpringBoot는 기본적으로 Build를 실행하면 bootJar 태스크는 실행하고 jar 태스크는 스킵해서 BootJar를 만들도록 되어 있다.

</br >

## web 모듈 build.gradle

```java
dependencies {
    compile project(path: ':matcherloper-core', configuration: 'default')

    implementation 'org.springframework.boot:spring-boot-starter-validation'
    implementation 'org.springframework.boot:spring-boot-starter-actuator'

    compile group: 'io.springfox', name: 'springfox-swagger2', version: '2.9.2'
    compile group: 'io.springfox', name: 'springfox-swagger-ui', version: '2.9.2'
}
```

web 모듈의 build.gradle을 보면 **compile project(path: ':matcherloper-core', configuration: 'default')** 가 보인다.

이는 web 모듈에서 core 모듈을 사용하기 위해 설정한 dependency이다.

</br > 

참고로 프로젝트에 QueryDsl 이 포함되어있다면 Spring boot 2.3.x 버전부터는 에러가 발생할 수 있다.

SpringBoot 2.3.x 버전에서 QueryDsl을 사용하기 위해 **configuration: 'default'**를 설정해주자.

 </br >

## 외부 모듈 Component Scan방법

### 방법 1

해당 프로젝트는 web 모듈을 통해 프로젝트를 실행시키기 때문에 web 모듈이 다른 모듈들을 의존하게 된다. 그렇기 때문에 web 모듈을 통해 web모듈 및 외부 모듈까지 Componen Scan을 진행시키면 된다.

이때 @SpringBootApplication어노테이션 달린 클래스는 최상위 패키지에 위치시킨다. 그리고 외부 모듈도 최상위 패키지 아래 코드를 작성해주면 된다.

</br >

### 방법 2

각각의 모듈들 마다 Component Scan할 수 있는 Config 클래스를 만든 후, @Import어노테이션을 통해 외부 모듈의 Component Scan을 진행하여 Bean을 초기화시킨다. 

두 번째 방법으로 진행하기로 했다. 방법 2가 더 유연하고 가독성이 좋다고 판단했기 때문이다.

## WebConfig.java

```java
@Configuration
@ComponentScan(basePackages = "com.toy.matcherloper.web")
@Import(value = {CoreConfig.class})
public class WebConfig {
}
```

## CoreConfig.java

```java
@Configuration
@ComponentScan(basePackages = "com.toy.matcherloper.core")
public class CoreConfig {
}
```

멀티 모듈을 적용해 보면서 많은 시행착오도 겪으며 많은 걸 배울 수 있었고, 어떻게 하면 의존성을 최소화할 수 있을지 생각해 볼 수 있는 좋은 기회였다.

