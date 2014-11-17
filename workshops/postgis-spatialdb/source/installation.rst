.. _installation:

Installation
============

We will be using OpenGeo Suite as the basis for this workshop. It includes all the parts we need to be able to demonstrate pushing SQL smarts out to the web.

* PostgreSQL, including 

  - PgAdmin III
  - PgShapeLoader
  - PostGIS

* GeoServer, including

  - OpenLayers

.. note::

  You will need administrative rights to your computer to install the database and web service software.

To get OpenGeo Suite installed, first 

* `Download <http://boundlessgeo.com/solutions/opengeo-suite/download/>`_ a copy (Windows/OSX), then
* `Install the Suite <http://suite.opengeo.org/4.1/installation/index.html>`_


Workshop Materials
------------------

The data to be installed are in the workshop materials bundle available from: http://data.opengeo.org/workshopmaterials/spdb-201411.zip. Within the zip file you will find:

**workshop/** 
  a directory containing this HTML workshop

**data/** 
  a directory containing the data files we will be loading and the original shape files

All the data in the package is public domain and freely re-distributable. This workshop is licensed as Creative Commons "`share alike with attribution <http://creativecommons.org/licenses/by-sa/3.0/us/>`_", and is freely re-distributable under the terms of that license. Our attribution requirement is that you retain the branding of the workshop materials.


Installing the Data
-------------------

GIS data comes in a large number of formats, and there are tools available for loading data from those formats into spatial databases. The most widely used tools are:

* `Feature Manipulation Engine (FME) <http://www.safe.com/fme>`_ a proprietary tool from Safe Software that can read from hundreds of formats and includes a GUI workbench for configuring translations.
* `ogr2ogr <http://www.gdal.org/ogr2ogr.html>`_ an open source command-line tool that can read from tens of formats. The FWTools installer included in the workshop software directory contains the ogr2ogr tool.

Rather than load the data directly from the original files (which are available in the "shapes.zip" file in the workshop bundle) we will load the data by restoring PostgreSQL dump files to our database.

First, start up the "PgAdmin" graphical administration tool for PostgreSQL. You can find it in the PostgreSQL application folder in the Start Menu.

.. image:: ./img/data_install_24.png

Double click the "PostgreSQL 8.3" entry in the list of servers (the entry with the red "X" on it).

You will be prompted for your database super-user password, and you can enter "postgres", since that's the value we chose back at the database installation step.

.. image:: ./img/data_install_25.png

Explore the database. You'll see a "medford" database, as well as a "postgres" database and a "template_postgis" database.  

The "template_postgis" database is a blank database with PostGIS already installed. It was created during the PostGIS install process. It is used when you want to create a new spatially enabled database -- in the database creation form, choose "template_postgis" as your template, and your new database will automatically be spatially enabled.

To load the Medford data, right-click on the "medford" database and select the "Restore..." option.

.. image:: ./img/data_install_26.png

In the restore form, click on the "..." to and navigate to the **data/** folder in the workshop bundle. Select the "medford.backup" file. When you are ready, click "OK" to start the data loading process. It may take a few minutes to load all the data.

.. image:: ./img/data_install_28.png

Click "OK" when the process is complete.

.. image:: ./img/data_install_30.png

When the restore is done, you will find a new schema named "medford" in your database. If you open it up you will find a collection of tables inside.

.. note:: 

  You may have to click the "refresh" button in PgAdmin (the circular arrows at top left) to refresh the database browser tree and see the new schema.

.. image:: ./img/data_install_29.png

Repeat the restore process to load the "geometry_columns.backup" file.

* Right-click on the "medford" database entry.
* Select the "Restore..." option.
* Navigate to the workshop folder and select "geometry_columns.backup" as the restore file.
* *Click the **Only data** option.*
* Press "OK" to start the restore.

.. image:: ./img/data_install_35.png

Repeat the restore process to load the "spatial_ref_sys.backup" file.

* Right-click on the "medford" database entry.
* Select the "Restore..." option.
* Navigate to the workshop folder and select "spatial_ref_sys.backup" as the restore file.
* *Click the **Only data** option.*
* Press "OK" to start the restore.

Installing Data with the Command Line
-------------------------------------

If you have an existing PostgreSQL / PostGIS installation (make sure your PostGIS >= 1.3.5) you can install the data by hand using the command line tools.

Create your database and spatially enable it:

::

  # createdb medford
  # createlang plpgsql medford
  # psql -f /path/to/lwpostgis.sql -d medford
  # psql -f /path/to/spatial_ref_sys.sql -d medford
  
Load the data files into the new database (note the "--data-only" argument in the last two commands):

::

  # pg_restore -d medford -U postgres medford.backup
  # pg_restore -d medford -U postgres --data-only geometry_columns.backup
  # pg_restore -d medford -U postgres --data-only spatial_ref_sys.backup  

Viewing the Medford Data
------------------------

You have spatial data loaded into your database! But how you can you tell?

You can browse the tables in the PgAdmin tool, and see the geometry values serialized as hexadecimal strings, but that is not very satisfying.

.. image:: ./img/udig_view_18.png

For a look at the data on a map, install the "uDig" software from the workshop **software/** folder. uDig is a desktop GIS viewing application, and it can view data in PostGIS and Oracle tables. No support for SQL Server, currently, though that is forthcoming.

Simply double-click the installer, accept all the defaults, and your install will be quickly complete.

.. image:: ./img/udig_view_07.png

Fire up uDig, and open the workbench. In the "Layer" menu, select the "Add.." option.

.. image:: ./img/udig_view_11.png

Choose PostGIS as the data source.

.. image:: ./img/udig_view_12.png

Fill in the connection parameters. The host is "localhost", the username is "postgres", the password is "postgres". The database is "medford", and **make sure** to change the schema to "medford" to. Click "Next".

.. image:: ./img/udig_view_14.png

Now select which tables you want to view, or, if you like, select all of them.

.. image:: ./img/udig_view_15.png

There's a lot of data, when you load up all the tables at the same time!

.. image:: ./img/udig_view_16.png

If you zoom in, though, you will see things begin to make sense. Explore the data a little and get a feel for what is in each table.

.. image:: ./img/udig_view_17.png

Installing Tomcat
-----------------

The Tomcat install for the workshop is not the default install you can `download <http://tomcat.apache.org/download-60.cgi>`_ from the Tomcat web site. We have taken Tomcat 6 and added in extra software and configuration for the workshop: 

* We have configured a JNDI database connection so that we can easily connect to our "medford" database in PostgreSQL.
* We have added a copy of GeoServer and configured it to pull layers from our "medford" database.
* We have added the scripts needed for our workshop examples.
* We have added a recent copy of OpenLayers.

To use Tomcat you will need a Java run-time installed first. You may already have one installed. You can check by opening up a terminal window (in Windows, choose "Run..." in the start menu, then type in "cmd") and running "java" at the command line. If you have Java installed, you will see some usage instructions. If you don't, you'll see an error message.

.. image:: ./img/tomcat_install_18.png

.. note::

  If you need to install Java, use the JRE included in the workshop software collection, "jre-6u13-windows-i586-p-s". Just double-click to start.

Once you have confirmed you have a working Java, or have installed Java, move on.
  
Unzip the "tomcat6-geoserver.zip" file included in the workshop software collection, to a location of your choosing (I like to put it at **C:\\**). Open a terminal window ("Run..." then "cmd") and navigate to the Tomcat directory. 

* Enter the "bin/" subdirectory
* Run "service.bat install" 
* Run "net start Tomcat6"

.. image:: ./img/tomcat_install_19.png

You should now have a running instance of Tomcat on your machine.

.. note::

  There are many ways for this process to go wrong. You might not have sufficient privileges to install the service: solution, use an account with those privileges. You might already have software using the network port (8080) that Tomcat uses: solution, stop that software, or reconfigure Tomcat to use a different port. Your firewall might disallow services opening on ports: solution, turn off your firewall, or make an exception for port 8080.
  
To test that Tomcat is now up and running, point your web browser at port 8080 on your local machine.

  http://localhost:8080/
  
You should see the Tomcat web server welcome page.

.. image:: ./img/tomcat_install_20.png

Uninstalling Tomcat
-------------------

To remove Tomcat from your system, you need to stop the service and then remove it from the registry.

* Open a terminal window
* Navigate to the Tomcat directory
* Enter the "bin/" subdirectory
* Run "net stop Tomcat6"
* Run "service.bat uninstall"

.. image:: ./img/tomcat_install_21.png

Now it is safe to delete the Tomcat directory from your hard drive.

Uninstalling PostgreSQL / PostGIS
---------------------------------

To remove PostgreSQL from your system, open the "Add or Remove Programs" tool from the "Control Panel" of your system. Choose the "Remove" option for PostGIS, then PostgreSQL.

.. image:: ./img/pgsql_install_22.png





_OpenGeo Suite: http://boundlessgeo.com/solutions/opengeo-suite/
_Suite installation instructions: http://suite.opengeo.org/opengeo-docs/installation/index.html
