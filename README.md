# openapi-generator-demo

Generate OpenApi (Swagger 2.0) Model

The goal of this project is to demo how a GSON-compatible POJO mode may be generated from a Swagger 2.0 JSON Schema utilizing a Maven plugin. This approach allows for modification of the schema without the need to manually code and re-code POJO classes. I have used the generated model in Kafka, MongoDB, and Mapstruct implementations without the need for any modification.  
In this example, I only need the model, so I exclude API generation and allow model generation only. This project is a single maven module, however typically you would pair this module with other service-oriented modules packaged together. Note: The output model POJOs are included in this project for reference only.  Typically, you would generate the model and .gitignore them, or else package as a jar and add as a dependency to other modules. Either way, best practice is to exclude the generated model from source control and allow the build to generate the classes.

Required Components:
- Swagger 2.0 model
- maven
- yaml editor (Swagger editor and/or KaiZen plugin for Eclipse)

How-to:
- For this example, I created a Spring Boot application using Spring Initializr.  
- Create a maven module with the source swagger file (typically src/main/resources...)
- Add the following dependencies to your maven pom file. The first brings in the swagger libraries, the second covers GSON compilation.
```xml
    <!-- https://mvnrepository.com/artifact/io.swagger/swagger-core -->
    <dependency>
        <groupId>io.swagger</groupId>
        <artifactId>swagger-core</artifactId>
        <version>1.5.3</version>
    </dependency>
    <dependency>
        <groupId>com.google.code.gson</groupId>
	<artifactId>gson</artifactId>
    </dependency>
```
- Configure your plugin.  For multiple models, create a separate <id>[unique execution name]</id> for each execution. In my case, I didn't have access to the Swagger flavor of DateTime so I forced generation with Java8.  Other configurations include excluding documentation and supporting files (for API, security, etc.)
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
							<inputSpec>${project.basedir}/src/main/resources/schema/demo-schema.yaml</inputSpec>
							<generatorName>java</generatorName>
							<configOptions>
								<sourceFolder>src/main/java</sourceFolder>
								<dateLibrary>java8</dateLibrary>
							</configOptions>
							<skipValidateSpec>false</skipValidateSpec>
							<output>${project.basedir}</output>
							<generatorName>java</generatorName>
							<modelPackage>com.sabre.model</modelPackage>
							<generateModels>true</generateModels>
							<generateApis>false</generateApis>
							<generateSupportingFiles>false</generateSupportingFiles>
							<generateModelTests>false</generateModelTests>
							<generateModelDocumentation>false</generateModelDocumentation>
						</configuration>
					</execution>
				</executions>
			</plugin>
```
- It's a good idea to clean up your model, so I added an execution that removes the model as part of the cleanup.  
```xml
            		<plugin>
				<artifactId>maven-clean-plugin</artifactId>
				<version>3.1.0</version>
				<configuration>
					<filesets>
						<fileset>
							<directory>src/main/java/com/sabre/model</directory>
							<includes>**/*</includes>
						</fileset>
					</filesets>
				</configuration>
			</plugin>
```
- Execute maven install.  Generated files will be built to the destination path and package specified in your configuration
