.. _l2i-format:

Output Format
=============

.. note::

  It is recommended to output the "improPhed" Level 2 data to the Level 2 directory, i.e. ``DIR_LOWER = DIR_HIGHER``.
  This way, the refined dataset is appended to the original dataset as a separate product.
  Thus, after running this submodule, two surface reflectance versions are available for each date. 
  The new product will have higher spatial resolution (more pixels) than the other products (e.g. ``QAI``).
  :ref:`higher-level` can digest this data structure, and the user can choose to use the original BOA or the refined product (``USE_L2_IMPROPHE``).


Data organization
^^^^^^^^^^^^^^^^^

The data are organized in a gridded data structure, i.e. data cubes.
The tiles manifest as directories in the file system, and the images are stored within.

.. seealso:: 

  Check out this `tutorial <https://davidfrantz.github.io/tutorials/force-datacube/datacube/>`_, which explains what a datacube is, how it is parameterized, how you can find a POI, how to visualize the tiling grid, and how to conveniently display cubed data.


Data Cube definition
^^^^^^^^^^^^^^^^^^^^

The spatial data cube definition is appended to each data cube, i.e. to each directory containing tiled datasets, see :ref:`datacube-def`.


Naming convention
^^^^^^^^^^^^^^^^^

Following 29-digit naming convention is applied to all output files:

Example filename: 20160823_LEVEL2_SEN2A_IMP.tif

+--------+-------+--------------------------------------------+
+ Digits + Description                                        +
+========+=======+============================================+
+ 1–8    + Acquisition date as YYYYMMDD                       +
+--------+-------+--------------------------------------------+
+ 10–15  + Product Level                                      +
+--------+-------+--------------------------------------------+
+ 17–21  + Sensor ID                                          +
+        +-------+--------------------------------------------+
+        + LND04 + Landsat 4 Thematic Mapper                  +
+        +-------+--------------------------------------------+
+        + LND05 + Landsat 5 Thematic Mapper                  +
+        +-------+--------------------------------------------+
+        + LND07 + Landsat 7 Enhanced Thematic Mapper         +
+        +-------+--------------------------------------------+
+        + LND08 + Landsat 8 Operational Land Imager          +
+--------+-------+--------------------------------------------+
+ 23–25  + Product Type                                       +
+        +-------+--------------------------------------------+
+        + IMP   + ImproPhed Bottom-of-Atmosphere Reflectance +
+--------+-------+--------------------------------------------+
+ 27–29  + File extension                                     +
+        +-------+--------------------------------------------+
+        + tif   + image data in compressed GeoTiff format    +
+        +-------+--------------------------------------------+
+        + dat   + image data in flat binary ENVI format      +
+        +-------+--------------------------------------------+
+        + hdr   + metadata for ENVI format                   +
+--------+-------+--------------------------------------------+


File format
^^^^^^^^^^^

The images are provided with signed 16bit datatype and band sequential (BSQ) interleaving in one of the following formats:

* GeoTiff 
  
  This is the recommended output option. 
  Images are compressed GeoTiff images using LZW compression with horizontal differencing.
  The images are generated with internal blocks for partial image access.
  These blocks are strips that are as wide as the ``TILE_SIZE`` and as high as the ``BLOCK_SIZE``.
  
* ENVI Standard format

  This produces flat binary images without any compression.
  This option might seem tempting as there is no overhead in cracking the compression when reading these data.
  However, the transfer of the larger data volume from disc to CPU often takes longer than cracking the compression.
  Therefore, we recommend to use the GeoTiff option.


Metadata
^^^^^^^^

Metadata are written to all output products.
For ENVI format, the metadata are written to the ENVI header (``.hdr`` extension).
For GeoTiff format, the metadata are written into the GeoTiff file.
If the metadata is larger than allowed by the GeoTiff driver, the excess metadata will be written to an "auxiliary metadata" file with ``.aux.xml`` extension.
FORCE-specific metadata will be written to the FORCE domain, and thus are probably not visible unless the FORCE domain (or all domains) are specifically printed:

.. code-block:: bash

  gdalinfo -mdd all 20160823_LEVEL2_SEN2A_BOA.tif


Product type
^^^^^^^^^^^^

* Reflectance

  There is only one product type, i.e. the ImproPhed Bottom-of-Atmosphere Reflectance (IMP). 
  The IMP product has the same specification as the BOA product (see :ref:`level2-format`), but spatial resolution was enhanced.
  The scale is 10000, and nodata value is -9999.
  IMP data contain multiple bands, which represent wavelengths, see metadata and following tables).
  All bands are provided at the same spatial resolution (see :ref:`l2-param`).

  