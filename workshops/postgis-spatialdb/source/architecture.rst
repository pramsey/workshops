.. _architecture:

Architecture
============

A modern "web map architecture", like ArcGIS Server or `OpenGeo Suite`_, combines a database with a map rendering engine, a middleware, a map cache, and a web client.  The API solutions promoted by Google and Microsoft provide developers access to just the web client, with some carefully chosen hooks back to middleware functions like geocoding or routing.

In all these approaches, the storage engine is abstracted away, behind a web services API of some sort. And that's unfortunate, because a spatial database provides much more than storage.

**A spatial database can perform all the analytical and query functions that define most "GIS" operations.**

There is a tendency in defining architectures to design for all possible futures, so one can end up with a design like this SOA architecture from ESRI:

.. image:: ./img/architecture-esri.png
   :class: inline

For this workshop, we are going to strip down the architecture to the database, a basic middle web tier, and a web client.  For visualization purposes, we'll keep map rendering functionality mostly in the middle tier, but we'll also explore some client side rendering.

.. image:: ./img/architecture-opengeo.png
   :class: inline

The idea is to keep the amount of software between the end user and the database as thin as we can, so we can really explore what is possible with the database alone. The web client will be used primarily to gather input data (in the form of clicks) and display outputs (as markers or table results), which is more aesthetically pleasing than just typing into a SQL terminal.

Tools
-----

The actual tools we will use are all bundled as a part of `OpenGeo Suite`_:

* PostgreSQL/PostGIS as the spatial database
* GeoServer for map rendering
* OpenLayers 3 as the map client
* PgAdmin as the database management tool



_OpenGeo Suite: http://boundlessgeo.com/solutions/opengeo-suite/
