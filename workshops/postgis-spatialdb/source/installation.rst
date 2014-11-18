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


Creating a Database
-------------------

We're going to need an empty database to store our workshop data in!

* `Create a spatial database`_ named ``medford`` to load data into.




Loading Spatial Data
--------------------

GIS data comes in a large number of formats, and there are tools available for loading data from those formats into spatial databases. The most widely used tools are:

* `ogr2ogr`_ an open source command-line tool that can read from tens of formats. The Suite "commandline tools" component includes the ogr2ogr tool. To batch load the shape data using `ogr2ogr`_ on the Linux command-line, try something like this::

    for f in *.shp; do
      echo "Loading $f into PostGIS"
      ogr2ogr \
        --config PG_USE_COPY YES \
        -a_srs 'EPSG:2914' \
        -lco GEOMETRY_NAME=geom \
        -lco FID=gid \
        -lco PRECISION=no \
        -nlt PROMOTE_TO_MULTI \
        -f PostgreSQL PG:'dbname=medford host=localhost' \
        $f
    done
  
* `shp2pgsql`_ a based command-line tool that reads from `shape files`_ and writes to PostGIS. Because it has fewer capabilities than `ogr2ogr`_ To batch load the shape data using `shp2pgsql`_, try something like this::

    for f in *.shp; do
      echo "Loading $f into PostGIS"
      shp2pgsql \
        -s 2914 \
        -D -i -I \
        -g geom \
        $f | psql -d medford
    done

* `pgShapeLoader`_ (also known as ``shp2pgsql-gui``) a basic GUI tool included with PostGIS that can batch load `shape files`_.

We'll use `pgShapeLoader`_ to load our spatial data into the ``medford`` database.






Viewing the Data
----------------

You have spatial data loaded into your database! But how you can you tell?

You can browse the tables in the PgAdmin tool, and see the geometry values serialized as hexadecimal strings, but that is not very satisfying.









.. _OpenGeo Suite: http://boundlessgeo.com/solutions/opengeo-suite/
.. _Suite installation instructions: http://suite.opengeo.org/opengeo-docs/installation/index.html
.. _Create a spatial database: http://suite.opengeo.org/opengeo-docs/dataadmin/pgGettingStarted/createdb.html
.. _pgShapeLoader: http://horizon.boundlessgeo.com/opengeo-docs/ee/dataadmin/pgGettingStarted/pgshapeloader.html
.. _shp2pgsql: http://suite.opengeo.org/4.1/dataadmin/pgGettingStarted/shp2pgsql.html
.. _ogr2ogr: http://www.gdal.org/ogr2ogr.html
.. _shape files: http://en.wikipedia.org/wiki/Shapefile