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