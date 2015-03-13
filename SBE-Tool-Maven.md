The SBE Tool currently does not have a dedicated Maven plugin; it is possible to run SBE builds 
using the code-haus plugins <code>exec-maven-plugin</code> and <code>build-helper-maven-plugin</code>

## Overview
To use the SBE tool as part of your Maven Build, you need to do the following:

1. Include SBE as a Dependency for the Project

1. Invoke SBE Build tool using Exec Plugin 

1. Include SBE Source Outputs to be compile via Maven 

1. Put your SBE Tool schema file in the location: src/main/resources/schema.xml

1. build i.e. mvn clean install 

Add the following to your POM dependencies: 

	<dependencies>
		<dependency>
			<groupId>uk.co.real-logic</groupId>
			<artifactId>sbe</artifactId>
			<version>1.0-RC2</version>
		</dependency>
	</dependencies>

Add the following to your build section 

    <build>
		<plugins>
			<plugin>
				<groupId>org.codehaus.mojo</groupId>
				<artifactId>build-helper-maven-plugin</artifactId>
				<version>1.1</version>
				<executions>
					<execution>
						<id>add-source</id>
						<phase>generate-sources</phase>
						<goals>
							<goal>add-source</goal>
						</goals>
						<configuration>
							<sources>
								<source>${project.build.directory}/generated-sources</source>
							</sources>
						</configuration>
					</execution>
				</executions>
			</plugin>
			<plugin>
				<groupId>org.codehaus.mojo</groupId>
				<artifactId>exec-maven-plugin</artifactId>
				<version>1.3.2</version>
				<executions>
					<execution>
						<phase>generate-sources</phase>
						<goals>
							<goal>java</goal>
						</goals>
					</execution>
				</executions>
				<configuration>
					<executableDependency>
						<groupId>uk.co.real-logic</groupId>
						<artifactId>sbe</artifactId>
					</executableDependency>
					<mainClass>uk.co.real_logic.sbe.SbeTool</mainClass>
					<systemProperties>
						<systemProperty>
							<key>sbe.output.dir</key>
							<value>${project.build.directory}/generated-sources</value>
						</systemProperty>
					</systemProperties>
					<arguments>
						<argument>${project.build.resources[0].directory}/schema.xml</argument>
					</arguments>
					<workingDirectory>${project.build.directory}/generated-sources</workingDirectory>
				</configuration>
				<dependencies>
					<dependency>
						<groupId>uk.co.real-logic</groupId>
						<artifactId>sbe</artifactId>
						<version>1.0-RC2</version>
					</dependency>
				</dependencies>
			</plugin>
		</plugins>
		<resources>
			<resource>
				<directory>src/main/resources</directory>
			</resource>
			<resource>
				<directory>target/generated-sources</directory>
			</resource>
		</resources>
	</build>

## Additional Parameters

Additional SBE parameters can be added in the System Properties section: 

	<systemProperty>
		<key>sbe.output.dir</key>
		<value>${project.build.directory}/generated-sources</value>
	</systemProperty>

The SBE tool parameters are documented here:

https://github.com/real-logic/simple-binary-encoding/wiki/Sbe-Tool-Guide