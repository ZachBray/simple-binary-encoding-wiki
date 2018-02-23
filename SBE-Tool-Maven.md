The SBE Tool does not have a dedicated Maven plugin, but it is possible to run SBE code generation 
using the MojoHaus [exec-maven-plugin](http://www.mojohaus.org/exec-maven-plugin/) and [build-helper-maven-plugin](http://www.mojohaus.org/build-helper-maven-plugin/).

## Overview
To use the SBE Tool as part of your Maven build:

1. Add [Agrona](https://github.com/real-logic/agrona) to your project ``<dependencies>``
1. Place your SBE schema file in a location of your choice (eg ``src/main/resources/schema.xml``)
1. Use ``exec-maven-plugin`` to invoke the SBE Tools code generator
1. Use ``build-helper-maven-plugin`` to include generated directories in your compilation classpath
1. Execute a goal that includes the ``generate-sources`` phase (most typically ``mvn clean install``)

You do not need to add SBE Tool itself to your project classpath, as all runtime requirements are provided by the Agrona dependency. You will only need SBE Tool as a dependency of the ``exec-maven-plugin``, as shown below.

The following configuration will implement the two MojoHaus plugins:

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
          <execution>
            <phase>generate-sources</phase>
            <goals>
              <goal>java</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <includeProjectDependencies>false</includeProjectDependencies>
          <includePluginDependencies>true</includePluginDependencies>
          <mainClass>uk.co.real_logic.sbe.SbeTool</mainClass>
          <systemProperties>
            <systemProperty>
              <key>sbe.output.dir</key>
              <value>${project.build.directory}/generated-sources/java</value>
            </systemProperty>
          </systemProperties>
          <arguments>
            <argument>${project.build.resources[0].directory}/schema.xml</argument>
          </arguments>
          <workingDirectory>${project.build.directory}/generated-sources/java</workingDirectory>
        </configuration>
        <dependencies>
          <dependency>
            <groupId>uk.co.real-logic</groupId>
            <artifactId>sbe-tool</artifactId>
            <version>1.7.7</version>
          </dependency>
        </dependencies>
      </plugin>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>build-helper-maven-plugin</artifactId>
        <version>3.0.0</version>
        <executions>
          <execution>
            <id>add-source</id>
            <phase>generate-sources</phase>
            <goals>
              <goal>add-source</goal>
            </goals>
            <configuration>
              <sources>
                <source>${project.build.directory}/generated-sources/java/</source>
              </sources>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

## Directory Structure
	pom.xml
	src/main/resources/schema.xml (your SBE schema)
	src/main/java (your main and test classes can all reference the SBE Tool-generated types)
	target/generated-sources/java (SBE Tool-generated sources)

## Use
The above configuration causes any goal that includes the ``generate-sources`` phase to generate the SBE schema-defined types. This occurs before your own classes are compiled, therefore enabling them to refer to the SBE-generated types. Some typical goals that include the ``generate-sources`` phase includes ``compile``, ``test`` and ``install``.

Any SBE Schema errors will be displayed in the console. You may wish to enable additional SBE Tool properties such as ``sbe.validation.xsd``, ``sbe.validation.stop.on.error`` and ``sbe.validation.warnings.fatal`` to halt the build on error.

## Additional Parameters
Additional SBE parameters can be added in the ``<systemProperties>`` element. The available parameters are documented in the [SBE Tool Guide](https://github.com/real-logic/simple-binary-encoding/wiki/Sbe-Tool-Guide).