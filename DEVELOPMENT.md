# Infrastructure

Production DB is Postgres 10.6 running on AWS

`lifedb-local`: Local DB via https://postgresapp.com/, running 10.9

Can use pgadmin4 to manage the DB: https://www.pgadmin.org/
* http://127.0.0.1:62773/browser/#

DB connections defined in `ships-api/prisma/schema.prisma`

# DB Organization

Postgres DB clusters are called "lifedb". This cluster stores a person's "personal record" in the same way an "electronic health record" stores everything about a person's health.

We're starting with just one database in "lifedb": "ships". The ships db will store everything related to relationships. We won't use the schemas feature (because Prisma does not provide an easy way to do so); just `public`. If we have app-specific technical data storage requirements (e.g. sync stamps), the tables will be prefixed with the app name (e.g. `contacts_sync_log`).

When we expand beyond just ships - e.g. to managing life data unrelated to relationships - we would add more databases to the cluster.

Keep in mind that 1 DB cluster ("lifedb") = 1 user. So everyone who wants to buy into this idea of storing their data needs to BYO-cluster. This makes sense because this is highly sensitive data which we should be responsible for managing ourselves.

# DB Migrations

`cd ships-api`
`prisma2 lift save`
`prisma2 lift up`

# Other architecture

The database is managed using the Prisma ORM suite. More specifically, Lift manages DB migrations (including generating the GraphQL schema) and Photon is the JavaScript/TypeScript-based ORM layer.

Apollo Server (to be implemented) is the application layer that exposes Photon's ORM functionality through a HTTP GraphQL API.

NB: this is bridged by generating Photon, which is tied to a specific DB/credentials, and using Photon within Apollo Server's resolvers.

# Work to do

I won't bother implementing/deploying Apollo Server until I have a use case for repeatable query or mutate logic that cannot be implemented with Retool UI's hooked into my Postgres DB. In other words, when I start to build some kind of apps on top of my data.

Step 1 is to put together the schema and generate all the CREATE statements and migrations for the Postgres DB.

Step 2 is to write some one-off scripts to seed some data (e.g. from FB, phone contacts, etc), and use Photon locally to push it up into my DB.

Step 3 is to expose my hacky data wrangled data in Retool, and build some UI's for managing it (including organizing it into circles).

Step 4 is to extend Retool's capabilities with some Lambda functions. For example, I could add "import" buttons that do a pull of Facebook friends. The Lambda functions could use Photon internally to make updates to the DB.

Only then might I start wanting Apollo Server -- if I'm building more of a live-app that needs to perform complex mutations and queries that don't have a 1-to-1 kind of "endpoint/action to static query/mutation" relationship.