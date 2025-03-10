---
title: "Configure PostgreSQL for Strapi on Platform.sh"
sidebarTitle: "PostgreSQL"
weight: -90
toc: false
description: |
  Configure your Strapi application to use a PostgreSQL database on Platform.sh.
---

Strapi can be configured to use PostgreSQL as its default database.
You can choose PostgreSQL when installing your app by selecting custom and PostgreSQL when asked for the installation type
or you can just configure your existing Strapi application to use PostgreSQL.
To configure a PostgreSQL database for Strapi on Platform.sh, follow these steps.

1. Install [PostgreSQL](https://www.postgresql.org/download/) on your machine
   and [pg](https://www.npmjs.com/package/pg) in your Strapi project.
   Pg comes installed with a fresh Strapi installation.

1. In your `services.yaml` file, add the following:

   ```yaml
   	postgres:
   	     type: postgresql:13
            disk: 512
   ```

1. In your `.platform.app.yaml` file, replace the relationship name to match the PostgreSQL database you added:

   ```yaml
   relationships:
      postgresdatabase: "postgres:postgresql"
   ```

1. In the `config` folder, locate the `database.js` file, and replace its content with the following:

   ```js
   const path = require("path");
   const config = require("platformsh-config").config();

   let dbRelationship = "postgresdatabase";

   // Strapi default sqlite settings.
   let connection = {
     connection: {
       client: "sqlite",
       connection: {
         filename: path.join(
           __dirname,
           "..",
           process.env.DATABASE_FILENAME || ".tmp/data.db"
         ),
       },
       useNullAsDefault: true,
     },
   };

   if (config.isValidPlatform() && !config.inBuild()) {
     // Platform.sh database configuration.
     const credentials = config.credentials(dbRelationship);
     console.log(
       `Using Platform.sh configuration with relationship ${dbRelationship}.`
     );

     let pool = {
       min: 0,
       max: 10,
       acquireTimeoutMillis: 600000,
       createTimeoutMillis: 30000,
       idleTimeoutMillis: 20000,
       reapIntervalMillis: 20000,
       createRetryIntervalMillis: 200,
     };

     connection = {
       connection: {
         client: "postgres",
         connection: {
           host: credentials.ip,
           port: credentials.port,
           database: credentials.path,
           user: credentials.username,
           password: credentials.password,
           ssl: false,
         },
         debug: false,
         pool,
       },
     };
   } else {
     if (config.isValidPlatform()) {
       // Build hook configuration message.
       console.log(
         "Using default configuration during Platform.sh build hook until relationships are available."
       );
     } else {
       // Strapi default local configuration.
       console.log(
         "Not in a Platform.sh Environment. Using default local sqlite configuration."
       );
     }
   }

   console.log(connection);

   // export strapi database connection
   module.exports = ({ env }) => connection;
   ```

This setting deploys your Strapi application with a PostgreSQL database.
If you don't specify a PostgreSQL service in your `services.yaml` file or the Strapi application is run on a local machine
the default SQLite database is used.
