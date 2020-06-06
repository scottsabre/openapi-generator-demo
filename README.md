# openapi-generator-demo

Generate OpenApi (Swagger 2.0) Model

The goal of this feature is to illustrate how a GSON-compatible POJO mode may be generated from a Swagger 2.0 JSON Schema utilizing a Maven plugin. This approach allows for modification of the Swagger model without the need to manually code and re-code POJO models. The generated model can be directly serialized/deserialized using KafkaTemplate without transformation or mapping.  In this example, I do not want the API, so I exclude API generation and focus on model generation only. This project is a single maven module, however typically you would pair this module with other (service) modules packaged under a parent. Note: Model POJOs are included in this project for reference only.  Typically, you would generate the model and .gitignore them, or else package as a jar and add as a dependency to other modules.

Required Components:
- Swagger 2.0 model
- maven
- yaml editor

How-to:
- Create a maven module with the source swagger file (typically src/main/resources...)
- Add the following dependency to your maven pom file:
```xml
    <!-- https://mvnrepository.com/artifact/io.swagger/swagger-core -->
    <dependency>
        <groupId>io.swagger</groupId>
        <artifactId>swagger-core</artifactId>
        <version>1.5.3</version>
    </dependency>
```
- Configure your plugin.  For multiple models, create a separate <id>[unique execution name]</id> for each execution. In my case, I didn't have access to the Swagger flavor of DateTime so I forced generation with Java8
```xml
    <plugin>
        <groupId>org.openapitools</groupId>
        <artifactId>openapi-generator-maven-plugin</artifactId>
        <!-- RELEASE_VERSION -->
        <version>3.3.4</version>
        <!-- /RELEASE_VERSION -->
        <executions>
            <execution>
                <goals>
                    <goal>generate</goal>
                </goals>
                <configuration>
                    <inputSpec>${project.basedir}/src/main/resources/schema/PetStore.yaml</inputSpec>
                    <generatorName>java</generatorName>
                    <configOptions>
                       <sourceFolder>src/gen/java/main</sourceFolder
                       <dateLibrary>java8</dateLibrary>
                    </configOptions>
                    <!-- Additional config settings -->
                    <output>$(project.basedir)</output>
                    <modelPackage>com.sabre.model</modelPackage>
                    <generateModels>true</generateModels>
                    <generateApis>false</generateApis>
                </configuration
            </execution>
        </executions>
    </plugin>
```
     
- Execute maven install.  Generated files will be built to the destination path and package specified in your configuration
