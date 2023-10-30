Quickstart with Docker
wget https://raw.githubusercontent.com/hasura/graphql-engine/stable/install-manifests/docker-compose/docker-compose.yaml
docker-compose up -d
docker ps

http://localhost:8080/console
   .. ou
http://localhost:8888/console

Run Hasura GraphQL engine using Docker
wget https://raw.githubusercontent.com/hasura/graphql-engine/stable/install-manifests/docker-run/docker-run.sh
chmod 777 docker-run.sh
./docker-run.sh
docker ps

Configure the docker-run.sh script
#! /bin/bash
docker run -d -p 8080:8080 --name hasura-api \
     -e HASURA_GRAPHQL_DATABASE_URL=postgres://username:password@hostname:port/dbname \
     -e HASURA_GRAPHQL_ENABLE_CONSOLE=true \
     hasura/graphql-engine:latest

Network config (Linux)
docker run -d --net=host \
     -e HASURA_GRAPHQL_DATABASE_URL=postgres://username:password@hostname:port/dbname \
     -e HASURA_GRAPHQL_ENABLE_CONSOLE=true \
     hasura/graphql-engine:latest

Securing the GraphQL endpoint (Docker)
#! /bin/bash
docker run -d -p 8080:8080 \
     -e HASURA_GRAPHQL_DATABASE_URL=postgres://username:password@hostname:port/dbname \
     -e HASURA_GRAPHQL_ENABLE_CONSOLE=true \
     -e HASURA_GRAPHQL_ADMIN_SECRET=myadminsecretkey \
     hasura/graphql-engine:latest

Postgres permissions
-- We will create a separate user and grant permissions on hasura-specific schemas and information_schema and pg_catalog. These permissions/grants are required for Hasura to work properly.

-- create a separate user for hasura
CREATE USER hasurauser WITH PASSWORD 'hasurauser';

-- create pgcrypto extension, required for UUID
CREATE EXTENSION IF NOT EXISTS pgcrypto;

-- create the schemas required by the hasura system
-- NOTE: If you are starting from scratch: drop the below schemas first, if they exist.
CREATE SCHEMA IF NOT EXISTS hdb_catalog;
CREATE SCHEMA IF NOT EXISTS hdb_views;

-- make the user an owner of system schemas
ALTER SCHEMA hdb_catalog OWNER TO hasurauser;
ALTER SCHEMA hdb_views OWNER TO hasurauser;

-- grant select permissions on information_schema and pg_catalog. This is required for hasura to query list of available tables
GRANT SELECT ON ALL TABLES IN SCHEMA information_schema TO hasurauser;
GRANT SELECT ON ALL TABLES IN SCHEMA pg_catalog TO hasurauser;

-- Below permissions are optional. This is dependent on what access to your tables/schemas - you want give to hasura. If you want expose the public schema for GraphQL query then give permissions on public schema to the hasura user. Be careful to use these in your production db. Consult the postgres manual or your DBA and give appropriate permissions.

-- grant all privileges on all tables in the public schema. This can be customised:
-- For example, if you only want to use GraphQL regular queries and not mutations,
-- then you can set: GRANT SELECT ON ALL TABLES...
GRANT USAGE ON SCHEMA public TO hasurauser;
GRANT ALL ON ALL TABLES IN SCHEMA public TO hasurauser;
GRANT ALL ON ALL SEQUENCES IN SCHEMA public TO hasurauser;

-- Similarly add this for other schemas, if you have any.
-- GRANT USAGE ON SCHEMA <schema-name> TO hasurauser;
-- GRANT ALL ON ALL TABLES IN SCHEMA <schema-name> TO hasurauser;
-- GRANT ALL ON ALL SEQUENCES IN SCHEMA <schema-name> TO hasurauser;


