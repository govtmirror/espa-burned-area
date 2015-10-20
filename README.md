## Burned-Area Version 2.1.0 Release Notes
Release Date: May 27, 2015

The burned area project contains application source code for producing burned area products.  The Burned Area (BA) Essential Climate Variable (ECV) is a set of procedures for detecting terrestrial burned areas from Landsat TM and ETM+ data.

This project is hosted by the US Geological Survey (USGS) Earth Resources Observation and Science (EROS) Land Satellite Data Systems (LSDS) Science Research and Development (LSRD) Project. For questions regarding this source code, please contact the Landsat Contact Us page and specify USGS CDR/ECV in the "Regarding" section. https://landsat.usgs.gov/contactus.php 

### Downloads
Burned Area source code

    git clone https://github.com/USGS-EROS/espa-burned-area.git

See git tag [version_2.1.0]

### Installation
  * Install dependent libraries - ESPA product formatter (https://github.com/USGS-EROS/espa-product-formatter), Boost, and OpenCV.
  * Set up environment variables.  Can create an environment shell file or add the following to your bash shell.  For C shell, use 'setenv VAR "directory"'.
```
    export PREFIX="path_to_directory_for_burned_area_build_data"
    export BOOST_INC="path_to_Boost_include_files"
    export BOOST_LIB="path_to_Boost_libraries"
    export OPENCVINC="path_to_modified_OpenCV_include_files"
    export OPENCVLIB="path_to_modified_OpenCV_libraries"
```

  * Download (from Github USGS-EROS espa-burned-area project) the source files. 

  * Modify the OpenCV code in modules/ml via the patch files that are provided in the src/boosted\_regression directory.  Then rebuild OpenCV with the patched files.  The patches were generated by using `diff -Naur $original_file $modified_file`
    * gbt.cpp  --> `cd modules/ml/src; patch < {BA_base}/src/boosted_regression/opencv_patches/opencv-2.4.x/gbt.cpp.patch`
    * ml.hpp  --> `cd modules/ml/include/opencv2/ml; patch < {BA_base}/src/boosted_regression/opencv_patches/opencv-2.4.x/ml.hpp.patch`
    * Rebuild OpenCV with the patched files

  * Install the source files. The following build will create burned area executables under $PREFIX/bin (tested in Linux with the gcc compiler). It will also copy the Python scripts from the scripts directory up the the $PREFIX/bin directory.
```
make (make -f Makefile.static for static builds)
make install
```
**Note that the Python scripts and C++ code rely on GDAL command-line executables, so GDAL will need to be installed and available in the $PATH.**

### Dependencies
  * ESPA raw binary and ESPA common libraries from ESPA product formatter and associated dependencies
  * XML2 library
  * Boost library
  * OpenCV library (modified with the patches from the boosted regression src directory)
  * GDAL command-line utilities

### Data Preprocessing
This version of the burned area application requires the input Landsat products to be in the ESPA internal file format.

### Data Postprocessing
After compiling the product-formatter raw\_binary libraries and tools, the convert\_espa\_to\_gtif and convert\_espa\_to\_hdf command-line tools can be used to convert the ESPA internal file format to HDF or GeoTIFF.  Otherwise the data will remain in the ESPA internal file format, which includes each band in the ENVI file format (i.e. raw binary file with associated ENVI header file) and an overall XML metadata file.

### Associated Scripts
Same scripts as for Version 1.0.0, updated for 1.1.0.  Added a script called do\_burn\_area.py which was developed to handle the end-to-end processing of burned area products.  The LPGS GeoTIFF products need to be processed to the ESPA internal file format (using convert\_espa\_to\_gtif), then the LEDAPS source code needs to be run to generate the TOA and Surface Reflectance bands.

### Verification Data

### User Manual
There are currently three applications associated with the burned area ECV.  The first application is the seasonal summaries, which generates the seasonal summaries and annual maximums for an input temporal stack of scenes for a given path/row.  This application determines the maximum geographic extents for the given stack, resamples each scene to this maximum geographic extent, then generates seasonal summaries for the reflective bands and index products (NBR, NBR2, NDVI, NDMI) along with a mask for the seasons and year.  This application also generates annual maximums for the index products.  (See the usage information via `process_temporal_ba_stack.py --help`.)

The second application is a scene-based probability mapping using a gradient boosting tree to predict the probability that any pixel is burned.  This application relies on the seasonal summaries and annual maximums generated in the previous step.  The appliction will create a single band product for the scene containing the burn probabilities.  (See the usage information via `do_boosted_regression.py --help`.)

The boosted regression code allows a model to be trained and saved so that it can be loaded later for model predictions.  The training and prediction can also occur during the same run, depending on the input parameters provided in the parameter file.  In order to run model predictions for any scene, the model must first have been trained and then loaded.

The burn\_threshold application filters the probability mappings as the third and final step in determining the burned pixels.  This is a two-part process where the thresholds are applied and then the overall annual burn summaries are generated.  (See the usage information via `do_threshold_stack.py --help` and `do_annual_burn_summaries.py --help`.)

The do\_burned\_area.py script runs the overall end-to-end processing given a stack of ESPA surface reflectance products in the ESPA raw binary format with associated XML files.  Thus each of the above applications are set up and invoked via this one overarching script.  (See the usage information via `do_burned_area.py --help`.)

### Product Guide

## Changes From Previous Version
#### Updates on May 27, 2015 - USGS EROS
  * burned-area
    1. Cleaned up code from the change in numpy and scikit environment for the Denver BA group.
    1. Added the ability to exclude high RMSE and high cloud scenes.  This exclusion is turned on in the do\_burned\_area.py script.
    1. Updated to use espa\_common.h vs. common.h.
    1. Updated to install in $PREFIX/bin vs. $BIN
