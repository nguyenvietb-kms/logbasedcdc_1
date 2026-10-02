# Architecture
<img width="701" height="362" alt="image" src="https://github.com/user-attachments/assets/6264dbb9-c27f-460e-be9f-a78a858062ca" />

# Steps to build the data streaming flow

## Create configuration files

* Create "config\connect-standalone.properties"
* Create "config\connect-postgres-source.properties"
* Create "config\connect-snowflake-sink.properties"

## Create "docker-compose.yml"

## Create "debezium-connector-postgres-2.5.4" plugin

Step 1: Remove Old Debezium JARs.

> rm -rf plugins/debezium-connector-postgres/*

Step 2: Download the Debezium 2.5.4.Final Archive.

> wget https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/2.5.4.Final/debezium-connector-postgres-2.5.4.Final-plugin.tar.gz

Step 3: Extract directly into your plugins Directory.

> tar -xzf debezium-connector-postgres-2.5.4.Final-plugin.tar.gz -C plugins/

Step 4: Clean up the Downloaded Archive.

> rm debezium-connector-postgres-2.5.4.Final-plugin.tar.gz

Step 5: Verify the Extracted Files.

> ls -l plugins/debezium-connector-postgres/

## Create Docker containers

Note: Run the following commands on WSL/Ubuntu.

Step 1: Launches or switches into your Ubuntu Linux environment within Windows Subsystem for Linux (WSL).

> ubuntu

Step 2: Changes your working directory to the specified folder containing your Docker setup files.

> cd docker-project/logbasedcdc

Step 3: Stops and removes all running containers, networks, and persistent volume data created by Docker Compose using administrator privileges.

> sudo docker-compose down -v

Step 4: Builds, creates, and starts all defined Docker containers in background (detached) mode.

> sudo docker-compose up -d

Step 5: Lists all currently active and running Docker containers along with their status and mapped ports.

> sudo docker ps

## Create "users" table on PostgreSQL

Step 1: Opens an interactive Linux command line (bash) inside the running Docker container named postgres-source-1 with root privileges.

> sudo docker exec -it  postgres-source-1 /bin/bash

Step 2: Logs into the PostgreSQL database console using the default superuser account named postgres.

> psql -U postgres

Step 3: A PostgreSQL meta-command that lists all existing tables in the current database.

> \dt

Step 4: Creates a new table named users with three columns: user_id (integer primary key), first_name, and last_name (text up to 200 characters).

> CREATE TABLE users(user_id INTEGER, first_name VARCHAR(200), last_name VARCHAR(200), PRIMARY KEY (user_id));

Step 5: Adds individual record rows into the users table (inserting user ID 1 for 'bao nguyen' and user ID 2 for 'john brown').

> INSERT INTO users VALUES(1, 'bao', 'nguyen');
> INSERT INTO users VALUES(2, 'john', 'brown');

## Launch Kafka Connect

This command launches Kafka Connect in standalone mode inside a running Docker container to stream data continuously from PostgreSQL to Snowflake.

> sudo docker exec -it connect connect-standalone \
>   /etc/kafka-connect/connect-standalone.properties \
>   /etc/kafka-connect/connect-postgres-source.properties \
>   /etc/kafka-connect/connect-snowflake-sink.properties

* sudo docker exec -it connect — Opens an interactive session in the running Docker container named connect.
* connect-standalone — Runs the Kafka Connect engine in single-worker mode (useful for development/testing).
* /etc/kafka-connect/connect-standalone.properties — Specifies the core Kafka Connect configuration (Kafka broker address, key/value converters).
* /etc/kafka-connect/connect-postgres-source.properties — Configures the Source Connector (Debezium) to capture data changes from PostgreSQL.
* /etc/kafka-connect/connect-snowflake-sink.properties — Configures the Sink Connector to take those changes from Kafka and write them into Snowflake.

## Check Snowflake

<img width="707" height="164" alt="image" src="https://github.com/user-attachments/assets/b6b43c28-08d4-4a24-8f08-9781af8f7bbb" />
