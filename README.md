```shell
mkdir my-orders-spring-boot
cd my-orders-spring-boot
```

Create file:
```shell
touch docker-compose.yml
```

Open and paste:
```yml
version: "3.1"

services:
  web:
    image: gradle:4.0.1-jdk8
    volumes:
    - .:/usr/src/my-orders-spring-boot
    working_dir: "/usr/src/my-orders-spring-boot"
```

```shell
docker-compose run --rm web bash
```

```shell
gradle init
```

```shell
exit
```

Open file `build.gradle` and paste:

```shell
buildscript {
	ext {
		springBootVersion = '1.5.6.RELEASE'
	}
	repositories {
		mavenCentral()
	}
	dependencies {
		classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
	}
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'org.springframework.boot'

jar {
    baseName = 'my-orders-spring-boot-docker'
    version =  '0.1.0'
}

version = '0.0.1-SNAPSHOT'
sourceCompatibility = 1.8

repositories {
	mavenCentral()
}

dependencies {
	compile('org.springframework.boot:spring-boot-starter-web')
	testCompile('org.springframework.boot:spring-boot-starter-test')
}
```

```shell
mkdir -p src/main/java/myorders/myorders/
touch src/main/java/myorders/myorders/MyOrdersApplication.java
```

Open file `MyOrdersApplication.java` and paste:

```java
package com.gabriellima.myorders.myorders;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
public class MyOrdersApplication {

	@RequestMapping("/")
	public String home() {
		return "Hello World";
	}

	public static void main(String[] args) {
		SpringApplication.run(MyOrdersApplication.class, args);
	}
}
```

Open again `docker-compose.yml` and edit to:

```yml
version: "3.1"

services:
  web:
    image: gradle:4.0.1-jdk8
    volumes:
      - .:/usr/src/my-orders-spring-boot
    working_dir: "/usr/src/my-orders-spring-boot"
    command: bash -c "gradle build && java -jar build/libs/my-orders-spring-boot-docker-0.1.0.jar"
    ports:
      - "8000:8080"
```

Now, just run:

```shell
docker-compose up
```