.. _installation:

Installation
============

We will be using OpenGeo Suite as the basis for this workshop. It includes all the parts we need to be able to demonstrate pushing SQL smarts out to the web.

* PostgreSQL, relational database, including 

  - PgAdmin III,
  - PgShapeLoader, and
  - PostGIS.

* GeoServer, spatial web application server, including

  - OpenLayers.

* QGIS, spatial desktop browser and editor.

.. note::

  You will need administrative rights to your computer to install the database and web service software.

To get OpenGeo Suite installed, first 

* `Download Suite <http://boundlessgeo.com/solutions/opengeo-suite/download/>`_  (Windows/OSX), then
* `Install the Suite <http://suite.opengeo.org/4.1/installation/index.html>`_ (Windows/OSX/Linux).
* `Download and install QGIS <http://boundlessgeo.com/solutions/solutions-software/qgis/qgis-download/>`_.

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

* `ogr2ogr <http://www.gdal.org/ogr2ogr.html>`_ an open source command-line tool that can read from tens of formats. The Suite "commandline tools" component includes the ogr2ogr tool.
* `Feature Manipulation Engine (FME) <http://www.safe.com/fme>`_ a proprietary tool from Safe Software that can read from hundreds of formats and includes a GUI workbench for configuring translations.

Rather than use these general purpose tools, we'll use a specific PostGIS tool for importing "`shape files <http://en.wikipedia.org/wiki/Shapefile>`_", PgShapeLoader (aka ``shp2pgsql-gui``).




that can only 

We will 
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







_OpenGeo Suite: http://boundlessgeo.com/solutions/opengeo-suite/
_Suite installation instructions: http://suite.opengeo.org/opengeo-docs/installation/index.html
