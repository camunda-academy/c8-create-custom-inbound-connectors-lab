> A new C8 inbound connector that monitors a specific folder in the file system to know if a file is created, modified or deleted inside it.
> 
> Read more about [creating Connectors](https://docs.camunda.io/docs/components/connectors/custom-built-connectors/connector-sdk/#creating-a-custom-connector)
> 
> Check out the [Connectors SDK](https://github.com/camunda/connector-sdk)

# File Watch Inbound Connector

Monitor directories for file creation, modification, or deletion and start process instances or catch intermediate events.

## Camunda Academy

This exercise solution is part of this course:

* [Camunda 8 - Create Custom Inbound Connectors](https://academy.camunda.com/camunda-8-create-custom-inbound-connectors)

## Build

You can package the Connector by running the following command:

```bash
mvn clean package
```

This will create the following artifacts:

- A thin JAR without dependencies.
- An uber JAR containing all dependencies, potentially shaded to avoid classpath conflicts. This will not include the SDK artifacts since those are in scope `provided` and will be brought along by the respective Connector Runtime executing the Connector.

### Shading dependencies

You can use the `maven-shade-plugin` defined in the [Maven configuration](./pom.xml) to relocate common dependencies
that are used in other Connectors and the [Connector Runtime](https://github.com/camunda-community-hub/spring-zeebe/tree/master/connector-runtime#building-connector-runtime-bundles).
This helps to avoid classpath conflicts when the Connector is executed. 

Use the `relocations` configuration in the Maven Shade plugin to define the dependencies that should be shaded.
The [Maven Shade documentation](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) 
provides more details on relocations.

## API

### Connector Properties

This Connector can be configured with the following properties:

| Name                                                     | Description                                    | Example                           |
|----------------------------------------------------------|------------------------------------------------|-----------------------------------|
| Event to monitor                                         | File event to monitor - Create, Modify, Delete | `Create`                          |
| Directory to monitor                                     | Directory in which to monitor events           | `/folderToMonitor`                |
| Polling interval                                         | Long polling interval in seconds               | `60`                              |
|Event variable                                            |Name of variable to store the contents of the inbound event| ``event``|
|Variable expression                                            |Expression to map elements of the inbound event to process variables| ``{fileName:event.fileName}``|
| Correlation key (process) - for intermediate catch event | Process variable to correlate incoming message | `fileName`           |
| Correlation key (payload) - for intermediate catch event | Correlation key in incoming message            | `event.fileName`                  |
| Activation condition      - for intermediate catch event | Condition under which the Connector triggers   | `event.fileName = 'someFileName'` |

### Output

This Connector produces the following output:

```json
{
  "event": {
    "monitoredEvent": "ENTRY_CREATE",
    "directory": "/folderToMonitor",
    "fileName": "someFileName"
  }
}
```

## Test locally

Run unit tests

```bash
mvn clean verify
```

### Test with local runtime

Use the [Camunda Connector Runtime](https://github.com/camunda-community-hub/spring-zeebe/tree/master/connector-runtime#building-connector-runtime-bundles) to run your function as a local Java application.

In your IDE you can also simply navigate to the `LocalContainerRuntime` class in test scope and run it via your IDE.
If necessary, you can adjust `application.properties` in test scope.

## Element Template

The element templates can be found in the [element-templates/](element-templates/) directory.

## Process Test

The process to test the connector can be found in the [src/main/resources/](src/main/resources) directory.
