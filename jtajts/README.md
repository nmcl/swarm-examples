# JAX-RS & Transactions

This example uses a JAX-RS resource and UserTransaction to start
and end transactions.

Note, this has now been folded into the official WildFly-Swarm repository maintained at https://github.com/wildfly-swarm

## Project `pom.xml`

The project is a normal maven project with `jar` packaging, not `war`.

    <packaging>jar</packaging>

The project adds a `<plugin>` to configure `wildfly-swarm-plugin` to
create the runnable `.jar`.

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

Additionally, the usual `maven-jar-plugin` is provided configuration
to indicate which of our own classes should be used for the `main()`
method.

    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-jar-plugin</artifactId>
        <configuration>
            <archive>
                <manifest>
                    <mainClass>org.wildfly.swarm.examples.transactions.Main</mainClass>
                </manifest>
            </archive>
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

    java -jar ./target/wildfly-swarm-example-transactions-1.0.0.Beta1-SNAPSHOT-swarm.jar


## Use

    http://localhost:8080/
    
The browser will print the message ...

    Active

Then try

    http://localhost:8080/begincommit

The browser output should be ...

    Transaction begun ok and committed ok

Next

    http://localhost:8080/beginrollback

And we'll see ...

    Transaction begun ok and rolled back ok

Finally try

    http://localhost:8080/nested

And you'll see ...

    Nested transaction support is not enabled!

Of course if you've enabled nested transactions in JTA on WildFly then you'll see something different!
