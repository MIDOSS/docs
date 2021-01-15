.. Copyright 2018-2021 The MIDOSS project contributors,
.. the University of British Columbia, and Dalhousie University.
..
.. Licensed under a Creative Commons Attribution 4.0 International License
..
..    https://creativecommons.org/licenses/by/4.0/


.. _TifFiles:

*********************************
Handling .tif files in Python
*********************************

We get ship exposure data in the form of tif files from Krista and Cam. We will make an enviroment with packages that can handle tif files and then explore our tif file in that enviroment.

Geotiff Enviroment
==================

We first create a conda enviroment with our tif specific packages.

.. code-block:: bash
   
    $ conda create -n geotiff -c conda-forge python=3.7 rasterio earthpy jupyterlab

This creates an enviroment called geotiff that has the packages rasterio, earthpy, and jupyterlab. We can use this enviroment by 

.. code-block:: bash
  
    $ conda activate geotiff

.tif File Explorations
======================

We will closely follow the tutorial shown `here`_


.. _here: https://www.earthdatascience.org/courses/earth-analytics-python/lidar-raster-data/open-lidar-raster-python/

