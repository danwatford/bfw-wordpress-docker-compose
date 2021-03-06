# Broadstairs Folk Week -  WordPress Docker Compose

Tools to local development of the Broadstairs Folk Week Website.

Based on https://github.com/nezhar/wordpress-docker-compose

Based on retrieved copies of the Broadstairs Folk Week website files and database, this docker-compose project can be used
to work on the website's custom theme and plugin, while using the latest wordpress files (including plugins) as used on the 
productioni site.

## Requirements

Docker and Docker Compose must be installed.

## Usage

Download the SQL dump of the live wordpress database and place in the wp-data directory.

Download the website files and place in the public_html directory.

Clone/checkout the Broadstairs Folk Week theme to the bfw-theme directory. You may need to create this directory.

Clone/checkout the Broadstairs Folk Week plugin to the bfw-plugin directory. You may need to create this directory.

The run the website:

```
docker-compose up
```

To stop the webiste:

```
docker-compose down
```

To remove the volumes associated with the project:

```
docker-compose down -v
```

## Changes to the upstream project (https://github.com/nezhar/wordpress-docker-compose)

The Broadstairs Folk Week website uses HTTPS. The wordpress image used by this project uses apache, but doesn't include
any SSL certificates. To work around this certificates will be installed as start up based on the technique described by
https://github.com/ogierschelvis in PR https://github.com/docker-library/wordpress/issues/46#issuecomment-358266189

When starting the wordpress plugin docker has been configured to run a bespoke script file, wp-init.sh, which installs
the certificates before launching apache.

## Fixing data

The database dump placed in wp-data will contain references to the live https://broadstairsfolkweek.org.uk website.

For development we want to modify that data to use references to localhost.

When the mysql container starts it will look for files in /docker-entrypoint-initdb.d, which is bind mounted from the
wp-data directory. Any .sh files are treated as scripts and sourced. Any .sql files are executed against the configured 
database.

A new script, 01-replace-urls.sh, has been added to wp-data to replace https://broadstairsfolkweek.org.uk in any .sql
files with http://localhsot.

By prefixing the script with 01 we should ensure it is run before the .sql file is imported into the database.
