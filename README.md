# OpenXPKI Docker Template

This repository contains a template for running OpenXPKI with the official debian packages. 
We also provide a docker-compose.yml for easy startup and management. 
This container is supposed to run behind an nginx reverse-proxy to provide https, 
therefore we provide a tested nginx configuration example.

This container is designed to run with MySql but can be changed to use other database systems.

# Quickstart

A quickstart can be used for testing as it contains everything that is needed.
This docker-compose contains default credentials for the database, that can be changed.
When starting the containers with docker-compose up, a database container is created and linked to openxpki as mysql.
The OpenXPKI sampleconfig.sh will be called after the database has been created and initialized.

    git clone https://github.com/DimeOne/docker-openxpki.git
    cd docker-openxpki
    docker-compose up -d && docker-compose logs -f
    
Start browser and navigate to http://127.0.0.1:8080

# Userdata

All userdata is either stored in the MySql database or as files in the openxpki configuration. When using the docker-compose.yml, 
all folders, including configuration, logs and MySql database files are stored as folders in the same directory as the docker-compose.yml.

  - mysql_data
  - config
  - logs

# Configuration

There are three parts of configuration that have to be considered. But will be created using defaults if not changed.

## Database configuration

This container is designed to run alongside a mysql container or atleast have the connection details configured using environment variables.
When using the docker-compose.yml, valid default values will be supplied, but should be changed before starting the containers the first time.

  - APP_DB_NAME=openxpki
  - APP_DB_HOST=mysql
  - APP_DB_PORT=3306
  - APP_DB_USER=openxpki
  - APP_DB_PASS=openxpki
  - APP_DB_ROOT_PASS=super-secret-password

The mysql port does not have to be exported when linked.

When these variables are set, values in database.yml will be overwritten by these.

APP_DB_ROOT_PASS is only required when creating a dabatase and can be omitted if the database already exists.

## PKI configuration

The configuration of the pki is done within /etc/openxpki.

If this folder doesn't contain a config.d folder, new example configuration files will be extracted to this directory.

When this container is started without parameters and no .initiated file in the config folder,
the default sampleconfig.sh will be run oncec to create and import new certificates.
The script sampleconfig.sh may be fine when running a demo but should be edited before being used in production.

To use a custom sampleconfig.sh, just create a createconfig.sh in the configuration directory, that will be called instead.

These files are used to configure OpenXPKI, consult http://openxpki.readthedocs.io/en/latest/ for further information.

## Nginx configuration

# Running & Commands

This container is expected to be linked to a MySql server or have the connection details passed through environment variables.

## create_db

Creates a new database from the given environment variables. Requires MySql root password to be set.

## init_db

Initiate the database with the mysql schema provided by openxpki.

## create_config

Create configuration certificates from sampleconfig.sh or createconfig.sh

## wait_for_db

Wait for a succesful database connection using credentials from environment variables.

## wait_for_db_root

Wait for a succesful database connection using root and credentials from environment variables.


# Environment Variables:

  - APP_DB_NAME = openxpki
  - APP_DB_HOST = mysql
  - APP_DB_PORT = 3306
  - APP_DB_USER = openxpki
  - APP_DB_PASS = openxpki
  - APP_DB_ROOT_PASS = super-secret-password


# ToDo:

  - So much documentation.
  - Generalize configuration for other dbs?


# Known Issues:
  - Unable to run openxpki start --foreground properly
    - See: https://github.com/openxpki/openxpki/issues/538
    - might cause problems with process reaping and leave zombies
    - prevents running the processes through an external supervisor like s6 or supervisord

# Sources:
  - http://openxpki.readthedocs.io/en/latest/
  - https://github.com/openxpki/openxpki
  - https://github.com/jetpulp/docker-openxpki
  - https://github.com/just-containers/s6-overlay

 