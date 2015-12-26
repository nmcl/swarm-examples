# JAX-RS & Arjuna Transactions

This example uses a JAX-RS resource and AtomicAction & friends.

Note, these are not Java EE compliant APIs and classes we're going to use. But that's the point!

## Project `pom.xml`

The project is a normal maven project with `war` packaging, not `jar`.

    <packaging>war</packaging>

The project adds a `<plugin>` to configure `wildfly-swarm-plugin` to
create the `.war`.

    <plugin>
      <groupId>org.wildfly.swarm</groupId>
      <artifactId>wildfly-swarm-plugin</artifactId>
      <version>${version.wildfly-swarm}</version>
      <executions>
        <execution>
          <phase>package</phase>
          <goals>
            <goal>create</goal>
          </goals>
        </execution>
      </executions>
    </plugin>

Additionally, the usual `maven-war-plugin` is provided.

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
		<configuration>
		  <failOnMissingWebXml>false</failOnMissingWebXml>
		</configuration>
            </plugin>

Currently we have to add javax.transactions into the pom.xml for building due to
https://github.com/wildfly-swarm/wildfly-swarm/issues/27

To define the needed parts of WildFly Swarm, a dependency is added

    <dependency>
        <groupId>org.wildfly.swarm</groupId>
        <artifactId>wildfly-swarm-jaxrs</artifactId>
        <version>${version.wildfly-swarm}</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.wildfly.swarm</groupId>
        <artifactId>wildfly-swarm-transactions</artifactId>
        <version>${version.wildfly-swarm}</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.wildfly.swarm</groupId>
        <artifactId>wildfly-swarm-msc</artifactId>
        <version>${version.wildfly-swarm}</version>
        <scope>provided</scope>
    </dependency>

## Build

    mvn package

## Run

   mvn wildfly-swarm:run

## Use

    First try ...

    http://localhost:8080/atomicaction

    In the browser you should see something like ...

    Begin BasicAction: 0:ffffac1c8001:2fb05ec4:567ed3a5:b status: ActionStatus.RUNNING
    Committed BasicAction: 0:ffffac1c8001:2fb05ec4:567ed3a5:b status: ActionStatus.COMMITTED

    Then try ...

    http://localhost:8080/begincommit

    Should result in ...

    Transaction begun ok and committed ok

    Next ...

    http://localhost:8080/beginrollback

    Should give you the following in the browser ...

    Transaction begun ok and committed ok
    
    Now try ...

    http://localhost:8080/beginrollback

    You should get something like the following in your browser ...

    Nested transaction  BasicAction: 0:ffffac1c8001:-315e47ea:567ed60e:19 status: ActionStatus.RUNNING started!
    