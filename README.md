# Quarkus demo: Hibernate ORM and RESTEasy

[Original link](https://quarkus.io/guides/hibernate-orm)

This is a minimal CRUD service exposing a couple of endpoints over REST,
with a front-end based on Angular so you can play with it from your browser.

While the code is surprisingly simple, under the hood this is using:
 - RESTEasy to expose the REST endpoints
 - Hibernate ORM to perform the CRUD operations on the database
 - A PostgreSQL database; see below to run one via Docker
 - ArC, the CDI inspired dependency injection tool with zero overhead
 - The high performance Agroal connection pool
 - Infinispan based caching
 - All safely coordinated by the Narayana Transaction Manager

## Requirements

To compile and run this demo you will need:

- JDK 11+
- GraalVM

In addition, you will need either a PostgreSQL database, or Docker to run one.

## Docker image
Use the docker-compose file

## Access the Database
```bash
docker exec -it PostgreSQL bash
su postgres
psql
```

### Configuring GraalVM and JDK 11+

Make sure that both the `GRAALVM_HOME` and `JAVA_HOME` environment variables have
been set, and that a JDK 11+ `java` command is on the path.

See the [Building a Native Executable guide](https://quarkus.io/guides/building-native-image)
for help setting up your environment.

## Building the demo

Launch the Maven build on the checked out sources of this demo:

> ./mvnw package

## Running the demo

### Live coding with Quarkus

The Maven Quarkus plugin provides a development mode that supports
live coding. To try this out:

> ./mvnw quarkus:dev

In this mode you can make changes to the code and have the changes immediately applied, by just refreshing your browser.

Hot reload works even when modifying your JPA entities.
Try it! Even the database schema will be updated on the fly.

### Run Quarkus in JVM mode

When you're done iterating in developer mode, you can run the application as a
conventional jar file.

First compile it:

> ./mvnw package

Next we need to make sure you have a PostgreSQL instance running (Quarkus automatically starts one for dev and test mode). To set up a PostgreSQL database with Docker:

> docker run --rm=true --name quarkus_test -e POSTGRES_USER=quarkus_test -e POSTGRES_PASSWORD=quarkus_test -e POSTGRES_DB=quarkus_test -p 5432:5432 postgres:13.3

Connection properties for the Agroal datasource are defined in the standard Quarkus configuration file,
`src/main/resources/application.properties`.

Then run it:

> java -jar ./target/quarkus-app/quarkus-run.jar

Have a look at how fast it boots.
Or measure total native memory consumption...

### Run Quarkus as a native application

You can also create a native executable from this application without making any
source code changes. A native executable removes the dependency on the JVM:
everything needed to run the application on the target platform is included in
the executable, allowing the application to run with minimal resource overhead.

Compiling a native executable takes a bit longer, as GraalVM performs additional
steps to remove unnecessary codepaths. Use the  `native` profile to compile a
native executable:

> ./mvnw package -Dnative

After getting a cup of coffee, you'll be able to run this binary directly:

> ./target/hibernate-orm-quickstart-1.0.0-SNAPSHOT-runner

Please brace yourself: don't choke on that fresh cup of coffee you just got.
    
Now observe the time it took to boot, and remember: that time was mostly spent to generate the tables in your database and import the initial data.
    
Next, maybe you're ready to measure how much memory this service is consuming.

N.B. This implies all dependencies have been compiled to native;
that's a whole lot of stuff: from the bytecode enhancements that Hibernate ORM
applies to your entities, to the lower level essential components such as the PostgreSQL JDBC driver, the Undertow webserver.

## See the demo in your browser

Navigate to:

<http://localhost:8080/index.html>

Have fun, and join the team of contributors!

## Running the demo in Kubernetes

This section provides extra information for running both the database and the demo on Kubernetes.
As well as running the DB on Kubernetes, a service needs to be exposed for the demo to connect to the DB.

Then, rebuild demo docker image with a system property that points to the DB. 

```bash
-Dquarkus.datasource.jdbc.url=jdbc:postgresql://<DB_SERVICE_NAME>/quarkus_test
```

## Running the demo using RedHat OpenShift
Install the PostgreSQL ephemeral and configure this service to have the following configuration

Create a new user, eg, `luiz`,`tech`.

```bash
CREATE USER luiz WITH PASSWORD 'tech';
GRANT ALL PRIVILEGES ON DATABASE hibernate_db TO luiz;
```

Deploy the application, using DevNation and add the following env variables

| # | Key                         | Value                                  |  
|---|-----------------------------|----------------------------------------|
| 1 | QUARKUS_DATASOURCE_JDBC_URL | jdbc:postgresql://postgresql:5432/hibernate_db |
| 2 | QUARKUS_DATASOURCE_USERNAME | user                                   |
| 3 | QUARKUS_DATASOURCE_PASSWORD | password                               |


### Access via OC 
Install the oc using brew
```bash
brew install openshift-cli
```

More references (click here)[https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/developer-cli-commands.html]


### References
* [PostgreSQL commands](https://tableplus.com/blog/2018/04/postgresql-how-to-grant-access-to-users.html)
* [RedHat DevNation](https://developers.redhat.com/devnation)
* [Quarkus](https://quarkus.io/guides/hibernate-orm)
