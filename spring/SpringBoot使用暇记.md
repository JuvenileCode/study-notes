## SpringBoot Use 

### SpringBoot  打JAR

- **xml增加插件**

  ```xml
  <build>
      <plugins>
          <plugin>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-maven-plugin</artifactId>
          </plugin>
      </plugins>
  </build>
  ```

- **进入pom.xml所在的目录，运行`mvn clean package -Dmaven.test.skip=true`命令打包**