# Ktor Configuration

> Application configuration that is necessary for the app to work.

The backend configuration uses a HOCON file which is found under the ``` application.conf ``` file in the resources directory. In there, the application categorises the configuration into several groups and uses environment variables to manage the app.

> Set the following environment variables before launching the app.

```bash
# Email configuration
EMAIL_HOST
SMTP_PORT
EMAIL_ACCOUNT
EMAIL_PASSWORD

# Do NOT leave these unchanged for production

# Authentication variables
JWT_PRIVATE
JWT_PUBLIC
JWT_ISS
OAUTH_PUBLIC
OAUTH_PRIVATE

# Database variables
DB_PORT
DB_PASSWORD
DB_USER
DB_DATABASE
DB_HOST

```

## Application configuration group

> General configuration

In this group, we can find several fields that are used for general configuration use cases. It also includes the dates under which the terms of service were updated last, this is neccessary as we need to show the user a message if the dates have changed. This is a very rough solution and needs to be updated, without having to restart the server.

## Database configuration group

> Database configuration

In this group the application gets the database connection information to connect to a database. **This is a very important group** as without it the app will not start.
By default the app will attempt to connect to a postgres database found locally on port ```55003``` with user ```postgres``` and password ```postgrespw```. The timeout of a database query is set to ```5 seconds``` by default, and the total connections in the pool are set to 3 (don't set this too high as the cloud host database won't be able to handle it).

## OAuth & JWT configuration

> Authentication configurations

These groups contain extremely sensitive information that handle the application's authentication module.
The private and public keys set in the configuration are **ONLY** to be used for convenience and definitely not used in production. the jwt issuer is set to the unkown IP address (0.0.0.0) as the cloud hosting service (render.com) wraps the container in its own network.

## Email configuration

> Email configurations used for sending emails

This group contains the email information that is going to be used when sending out emails. This group does **NOT** have any defaults set, please set this up before launching the app.

## DNS Configuration

> The app has a DNS configured under `IONOS.co.uk` to redirect traffic to the cloud hosting provider. The DNS provider also handles the email service and SSL certificates.
