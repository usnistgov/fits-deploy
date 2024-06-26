# FITS Docker Configuration

Docker Compose file for deploying FITS and dependent services

## Services

### 1. FITS Web Application (fits-webapp)
**Image:** nist775hit/fits-webapp\
Container running FITS web application on a tomcat server on port **8080**\
Port **8080** is exposed from this container to port **8080**

**Depends On**
* FHIR Adapter (fits-fhir-adapter)
* MongoDB (fits-mongodb)

**Environment Variables**

FITS Web App reads environment variables to configure the instance,
when using the provided docker-compose files, the environment variables are read from file "fits-webapp-config.env" in the same folder as the docker compose file.

| Variable Name | Description |  |
|---|---|---|
| FITS_MONGO_HOST | The host of the mongodb instance | **Required (has default in docker compose file)** -  Default value is set to "fits-mongodb" when using this docker compose file |
| FITS_MONGO_PORT | The port of the mongodb instance | **Required (has default in docker compose file)** -  Default value is set to "27017" when using this docker compose file
| FITS_MONGO_DB_NAME | The database name | **Required (has default in docker compose file)** -  Default value is set to "cdsi-db" when using this docker compose file
| FITS_CREATE_ADMIN_IF_NOT_EXIST | true/false | if set to true, an admin user (username "admin") will be created on startup if it doesn't exist 
| FITS_ADMIN_PASSWORD | admin user password when creating a new admin on startup (FITS_CREATE_ADMIN_IF_NOT_EXIST) | **Required when FITS_CREATE_ADMIN_IF_NOT_EXIST=true**
| FITS_ADMIN_EMAIL | admin user email when creating a new admin on startup (FITS_CREATE_ADMIN_IF_NOT_EXIST) | **Required when FITS_CREATE_ADMIN_IF_NOT_EXIST=true**
| FITS_WEB_ADMIN_EMAIL | Email address for users to contact - shown in the footer of the web application as "mailto:...." |
| FITS_EMAIL_HOST | Host of the email server to use when sending notifications | If not specified email notifications and email dependent user management features will not be supported
| FITS_EMAIL_PORT | Port of the email server | **Required (has default in web app)** default value is set to 25
| FITS_EMAIL_PROTOCOL | Email protocol (e.g. smtp) |
| FITS_EMAIL_SMTP_AUTH | true/false - if email server requires authentication | NOTE: only false is supported as of now
| FITS_EMAIL_FROM | The "from" email address when sending notifications |
| FITS_EMAIL_SUBJECT | The "subject" in email notifications sent from FITS | Default is set to "FITS Notification"
| FITS_ADAPTER_URL | URL of the FHIR Adapter | **Required (has default in docker compose file)** Default value is set to 'http://fits-fhir-adapter:8080/fhirAdapter/fhir/Parameters/$cds-forecast'

### 2. FHIR Adapter (fits-fhir-adapter)
**Image:** nist775hit/fits-fhir-adapter\
Container running FITS FHIR Adapter on a tomcat server on port **8080**

### 3. Mongo DB (fits-mongodb)
**Image:** mongodb/mongodb-community-server:4.4.29-ubuntu2004\
Container running the mongodb instance hosting the database for FITS on port **27017**

**Data persistence between instances**\
MongoDB persists data to folder **/data/db/** in this container, to persist data to the host environment you can mount the **/data/db/** folder to a local folder in the host (local_path)

```yaml
  [...]
  fits-mongodb:
    image: mongodb/mongodb-community-server:4.4.29-ubuntu2004
    volumes:
      - local_path:/data/db/
```

## Getting Started

1. Set up the environment by modifying the fits-webapp-config.env file
   1. Configure admin user (Recommended for first use - discard the variables after admin user is created after first use)
   2. Configure email server (Optional)
2. Mount a volumn to "fits-mongodb" service to persist the database outside the container (see above service fits-mongodb)
3. Make sure port 8080 is available on host machine
4. Run `docker compose up`
5. You can run `docker compose down` to shutdown the services