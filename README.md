# slammAutomation
Scripts and instructions for automating the SLAMM Model.

The scripts used in this project work with ArcGIS Pro (ArcGIS Notebooks) and Jupter Notebooks, but the mehtods can be applied to any GIS and script editing environments. 

The scripts are created with Python 3 language.

The files will enable someone to automate the SLAMM model for the purpose of repeated Sea Level Rise analysis. 

You will need to download the latest SLAMM release from Warren Pinnacle.

First, decide on a study area and gather your data. The minimum requirements for SLAMM are a DEM raster, a Slope raster (dervied from the DEM), and a land cover raster. I chose to create the land cover raster by combining the National Wetlands Inventory (NWI) and National Land Cover Database (NLCD) datasets into one raster. 

Throughout the batches of code, the folder paths will need to fixed for your computer. 

Next, the data must be processed before using it in SLAMM. Make sure you have a shapfile or feature class that contains the boundary of your study area. Before preprocessing the data, you must attribute the NWI dataset (if you're using it) with the corresponding SLAMM code (see SLAMM 4 Technical Documentation, page 43).
The first set of scripts will take the input data and process them to align with SLAMM requirements. This means the data must be clipped, converted to raster as necessary, projected, and converted to ASCII format. 
Data preprocessing scripts are run inside ArcGIS Notebooks.
This is the code for data preprocessing:
"""
Generated by ArcGIS ModelBuilder on : 2021-07-01 17:12:04
"""
import arcpy

def DataPreprocessing():  # DataPreprocessing

    # To allow overwriting outputs change overwriteOutput option to True.
    arcpy.env.overwriteOutput = False

    # Check out any necessary licenses.
    arcpy.CheckOutExtension("3D")
    arcpy.CheckOutExtension("spatial")

    # Model Environment settings
    with arcpy.EnvManager(scratchWorkspace=r"C:\path.gdb", workspace=r"C:\path.gdb"):
        Wetlands = "path"
        dem = arcpy.Raster("dem")
        studyAreaBoundary = "StudyArea"
        Wetlands_2_ = "Wetlands"
        NLCD_2016_Land_Cover_img = "C:path\\NLCD_2016_Land_Cover.img"
        NLCD_2016_Land_Cover_2_ = arcpy.Raster("NLCD_2016_Land_Cover.img")
        modelBuilder_test = "C:\\path\\modelBuilder_test"

        # Process: Project Raster (Project Raster) (management)
        DEM_projected = "C:\\path.gdb.gdb\\dem_projected"
        with arcpy.EnvManager(scratchWorkspace=r"C:\path.gdb", workspace=r"C:\path.gdb"):
            arcpy.management.ProjectRaster(in_raster=statewidedem, out_raster=DEM_projected, out_coor_system="PROJCS['Albers_Conical_Equal_Area',GEOGCS['GCS_WGS_1984',DATUM['D_WGS_1984',SPHEROID['WGS_1984',6378137.0,298.257223563]],PRIMEM['Greenwich',0.0],UNIT['Degree',0.0174532925199433]],PROJECTION['Albers'],PARAMETER['false_easting',0.0],PARAMETER['false_northing',0.0],PARAMETER['central_meridian',-96.0],PARAMETER['standard_parallel_1',29.5],PARAMETER['standard_parallel_2',45.5],PARAMETER['latitude_of_origin',23.0],UNIT['Meter',1.0]]", resampling_type="NEAREST", cell_size="30 30", geographic_transform=[], Registration_Point="", in_coor_system="PROJCS['Clarke_1866_UTM_Zone_17N',GEOGCS['GCS_Clarke_1866',DATUM['D_Clarke_1866',SPHEROID['Clarke_1866',6378206.4,294.9786982]],PRIMEM['Greenwich',0.0],UNIT['Degree',0.0174532925199433]],PROJECTION['Transverse_Mercator'],PARAMETER['False_Easting',500000.0],PARAMETER['False_Northing',0.0],PARAMETER['Central_Meridian',-81.0],PARAMETER['Scale_Factor',0.9996],PARAMETER['Latitude_Of_Origin',0.0],UNIT['Meter',1.0]]", vertical="NO_VERTICAL")
            dem_projected = arcpy.Raster(DEM_projected)

        # Process: Slope (Slope) (3d)
        slope = "C:\\path.gdb.gdb\\slope"
        with arcpy.EnvManager(scratchWorkspace=r"C:\path.gdb", workspace=r"C:\path.gdb"):
            arcpy.ddd.Slope(in_raster=DEM_projected, out_raster=statewide_slope, output_measurement="DEGREE", z_factor=1, method="PLANAR", z_unit="METER")
            slope = arcpy.Raster(slope)

        # Process: Clip Raster (Clip Raster) (management)
        slope_Clipped = "C:\\path.gdb.gdb\\statewide_slope_Clipped"
        with arcpy.EnvManager(scratchWorkspace=r"C:\path.gdb", workspace=r"C:\path.gdb"):
            arcpy.management.Clip(in_raster=statewide_slope, rectangle="1442072.171875 1167173.42987061 1543286.30389404 1260625.32250977", out_raster=statewide_slope_Clipped, in_template_dataset=studyAreaBoundary, nodata_value="", clipping_geometry="ClippingGeometry", maintain_clipping_extent="NO_MAINTAIN_EXTENT")
            slope_Clipped = arcpy.Raster(slope_Clipped)

        # Process: Raster to ASCII (Raster to ASCII) (conversion)
        slope_asc = "C:\\path\\slope.asc"
        with arcpy.EnvManager(scratchWorkspace=r"C:\path.gdb", workspace=r"C:\path.gdb"):
            arcpy.conversion.RasterToASCII(in_raster=slope_Clipped, out_ascii_file=slope_asc)

        # Process: Clip Raster (2) (Clip Raster) (management)
        DEM_projected_Clipped = "C:\\path.gdb.gdb\\DEM_projected_Clipped"
        with arcpy.EnvManager(scratchWorkspace=r"C:\path.gdb", workspace=r"C:\path.gdb"):
            arcpy.management.Clip(in_raster=DEM_projected, rectangle="1442072.171875 1167173.42987061 1543286.30389404 1260625.32250977", out_raster=statewideDEM_projected_Clipped, in_template_dataset=studyAreaBoundary, nodata_value="", clipping_geometry="ClippingGeometry", maintain_clipping_extent="NO_MAINTAIN_EXTENT")
            statewideDEM_projected_Clipped = arcpy.Raster(statewideDEM_projected_Clipped)

        # Process: Raster to ASCII (2) (Raster to ASCII) (conversion)
        dem_asc = "C:\\path\\dem.asc"
        with arcpy.EnvManager(scratchWorkspace=r"C:\path.gdb", workspace=r"C:\path.gdb"):
            arcpy.conversion.RasterToASCII(in_raster=DEM_projected_Clipped, out_ascii_file=dem_asc)

        # Process: Project (2) (Project) (management)
        Wetlands_Projected = "C:\\path.gdb.gdb\\Wetlands_Projected"
        with arcpy.EnvManager(scratchWorkspace=r"C:\path.gdb", workspace=r"C:\path.gdb"):
            arcpy.management.Project(in_dataset=Wetlands_2_, out_dataset=SC_Wetlands_Projected, out_coor_system="PROJCS['Albers_Conical_Equal_Area',GEOGCS['GCS_WGS_1984',DATUM['D_WGS_1984',SPHEROID['WGS_1984',6378137.0,298.257223563]],PRIMEM['Greenwich',0.0],UNIT['Degree',0.0174532925199433]],PROJECTION['Albers'],PARAMETER['false_easting',0.0],PARAMETER['false_northing',0.0],PARAMETER['central_meridian',-96.0],PARAMETER['standard_parallel_1',29.5],PARAMETER['standard_parallel_2',45.5],PARAMETER['latitude_of_origin',23.0],UNIT['Meter',1.0]]", transform_method=["WGS_1984_(ITRF00)_To_NAD_1983"], in_coor_system="PROJCS['NAD_1983_Albers',GEOGCS['GCS_North_American_1983',DATUM['D_North_American_1983',SPHEROID['GRS_1980',6378137.0,298.257222101]],PRIMEM['Greenwich',0.0],UNIT['Degree',0.0174532925199433]],PROJECTION['Albers'],PARAMETER['False_Easting',0.0],PARAMETER['False_Northing',0.0],PARAMETER['Central_Meridian',-96.0],PARAMETER['Standard_Parallel_1',29.5],PARAMETER['Standard_Parallel_2',45.5],PARAMETER['Latitude_Of_Origin',23.0],UNIT['Meter',1.0]]", preserve_shape="NO_PRESERVE_SHAPE", max_deviation="", vertical="NO_VERTICAL")

        # Process: Polygon to Raster (Polygon to Raster) (conversion)
        Wetlands_Raster = "C:\\path.gdb.gdb\\Wetlands_Raster"
        with arcpy.EnvManager(scratchWorkspace=r"C:\path.gdb", workspace=r"C:\path.gdb"):
            arcpy.conversion.PolygonToRaster(in_features=Wetlands_Projected, value_field="slammCode", out_rasterdataset=Wetlands_Raster, cell_assignment="CELL_CENTER", priority_field="NONE", cellsize=NLCD_2016_Land_Cover_img, build_rat="BUILD")

        # Process: Clip Raster (4) (Clip Raster) (management)
        Wetlands_Raster_Clipped = "C:\\path.gdb.gdb\\Wetlands_Raster_Clipped"
        with arcpy.EnvManager(scratchWorkspace=r"C:\path.gdb", workspace=r"C:\path.gdb"):
            arcpy.management.Clip(in_raster=Wetlands_Raster, rectangle="1442072.171875 1167173.42987061 1543286.30389404 1260625.32250977", out_raster=Wetlands_Raster_Clipped, in_template_dataset=studyAreaBoundary, nodata_value="", clipping_geometry="ClippingGeometry", maintain_clipping_extent="NO_MAINTAIN_EXTENT")
            Wetlands_Raster_Clipped = arcpy.Raster(Wetlands_Raster_Clipped)

        # Process: Clip Raster (3) (Clip Raster) (management)
        NLCD_ClippeD = "C:\\path.gdb.gdb\\NLCD_ClippeD"
        with arcpy.EnvManager(scratchWorkspace=r"C:\path.gdb", workspace=r"C:\path.gdb"):
            arcpy.management.Clip(in_raster=NLCD_2016_Land_Cover_img_2_, rectangle="1442072.171875 1167173.42987061 1543286.30389404 1260625.32250977", out_raster=NLCD_ClippeD, in_template_dataset=studyAreaBoundary, nodata_value="255", clipping_geometry="ClippingGeometry", maintain_clipping_extent="NO_MAINTAIN_EXTENT")
            NLCD_ClippeD = arcpy.Raster(NLCD_ClippeD)

        # Process: Reclassify (Reclassify) (3d)
        NLCD_Reclassified = "C:\\path.gdb.gdb\\NLCD_Reclassified"
        with arcpy.EnvManager(scratchWorkspace=r"C:\path.gdb", workspace=r"C:\path.gdb"):
            arcpy.ddd.Reclassify(in_raster=NLCD_ClippeD, reclass_field="Value", remap="0 21 NODATA;21 24 1;25 30 NODATA;31 2;32 41 NODATA;42 43 2;44 51 NODATA;52 2;53 70 NODATA;71 2;72 81 NODATA;82 2;83 95 NODATA", out_raster=NLCD_Reclassified, missing_values="NODATA")
            NLCD_Reclassified = arcpy.Raster(NLCD_Reclassified)

        # Process: Mosaic To New Raster (Mosaic To New Raster) (management)
        with arcpy.EnvManager(extent="1442069.5572855 1167159.6686969 1543289.5572855 1260639.6686969", scratchWorkspace=r"C:\path.gdb", workspace=r"C:\path.gdb"):
            rasterMosaic_tif = arcpy.management.MosaicToNewRaster(input_rasters=[Wetlands_Raster_Clipped, NLCD_Reclassified], output_location=modelBuilder_test, raster_dataset_name_with_extension="rasterMosaic.tif", coordinate_system_for_the_raster="PROJCS['Albers_Conical_Equal_Area',GEOGCS['GCS_WGS_1984',DATUM['D_WGS_1984',SPHEROID['WGS_1984',6378137.0,298.257223563]],PRIMEM['Greenwich',0.0],UNIT['Degree',0.0174532925199433]],PROJECTION['Albers'],PARAMETER['false_easting',0.0],PARAMETER['false_northing',0.0],PARAMETER['central_meridian',-96.0],PARAMETER['standard_parallel_1',29.5],PARAMETER['standard_parallel_2',45.5],PARAMETER['latitude_of_origin',23.0],UNIT['Meter',1.0]]", pixel_type="8_BIT_UNSIGNED", cellsize=None, number_of_bands=1, mosaic_method="LAST", mosaic_colormap_mode="FIRST")[0]
            rasterMosaic_tif = arcpy.Raster(rasterMosaic_tif)

        # Process: Raster to ASCII (3) (Raster to ASCII) (conversion)
        landcover_asc = "C:\\path\\landcover.asc"
        with arcpy.EnvManager(scratchWorkspace=r"C:\path.gdb", workspace=r"C:\path.gdb"):
            arcpy.conversion.RasterToASCII(in_raster=rasterMosaic_tif, out_ascii_file=landcover_asc)

if __name__ == '__main__':
    DataPreprocessing()

After data preprocessing, the data must be analyzed with SLAMM. Before analysis, create a SLAMM parameter file by running through the SLAMM interface with your input ASCII files, and creating a .txt parameter file. Check out the SLAMM documentation for more information on working with SLAMM. 
This set of code uses the .txt parameter file to automate SLAMM for various SLR rates, but the concepts can be reworked to reflect your study interests (like changing timelines or study areas).
This set of code is run inside Jupyter Notebooks. 
This is the code for data analysis:

import time
time_start = time.time()

import os 
import subprocess
import sys

#Start this workflow with an already created parameter file. 

#Edit the parameter file to run immediately. 
parameter_file = open(r"path\slammInputData\parameter.txt", "r")
list_of_lines = parameter_file.readlines()
list_of_lines[974] = "ExecuteImmediately:TRUE\n"
parameter_file = open(r"path\slammInputData\parameter.txt", "w")
parameter_file.writelines(list_of_lines)
parameter_file.close()

#Write a batch file to run SLAMM.
slammBat = open("path\\batch.bat", "w+")
slammBat.write(r"path\slamm6" + ' "' + r"path\slammInputData\parameter.txt" + '"')
slammBat.close()

#Edit parameter file to run for each SLR scenario from 1 to 2 meters at a 0.05 increase.
x = 1.00
while x < 3.05:
    #change the sea level rise rate for each interval
    list_of_lines[911] = "CustomSLRArray[i]: " + str(x) + "000000000000E+0000\n"
    list_of_lines[913] = "Display_Screen_Maps:FALSE\n"
    list_of_lines[915] = "RunFirstYear:FALSE\n"
    parameter_file = open(r"path\slammInputData\charleston.txt", "w")
    parameter_file.writelines(list_of_lines)
    parameter_file.close()
    #run SLAMM
    subprocess.call(["path\\batch.bat"])
    #add 0.05 for the next interval
    x = x + 0.05

#print how long the program took:
endTime = time.time() - time_start
print("The program took " + str(int(endTime)) + " seconds. \nOr " + str(int(endTime/60)) + " minutes.")

The next set of code post processes the data to create numeric results. 
The output SLAMM maps are calculated to understand how land cover changes. 
This code is set up to work with ArcGIS Notebooks.
The scripts will create an output Excel file.
This is the code for data post processing:

#Workflow to Determine Marshland Loss using SLAMM Outputs

import arcpy

#Use a SLAMM output from year of data to establish a baseline.

#create a varaible for the raster layer
marsh2016 = r"slammOutputs\dem_OUT, 2016, 1 meters _GIS.ASC"

#conver the raster to a feature class
marsh2016 = arcpy.conversion.RasterToPolygon(marsh2016, r'output.gdb\marsh2016', "SIMPLIFY", "Value", "SINGLE_OUTER_PART", None)

#calculate the area of each land cover category
marsh2016 = arcpy.management.AddGeometryAttributes(marsh2016, "AREA", '', "SQUARE_KILOMETERS", None)

#run a summary statistics process to sum the area for each land category
arcpy.analysis.Statistics(marsh2016, r'output.gdb\marsh2016_Statistics', "POLY_AREA SUM", "gridcode")

arcpy.management.SelectLayerByAttribute("marsh2016_Statistics", "NEW_SELECTION", "gridcode = 8", None)

with arcpy.da.SearchCursor("marsh2016_Statistics", ['SUM_POLY_AREA']) as cursor:
    for row in cursor:
        originalMarsh = row
        print('There are roughly ' + str(int(row[0])) + ' square kilometers of marshland in the study area.')
        
#Now repeat this process for each sea level rise scenario.

#create a variable for the interval of sea level rise
x = 1

#variable for naming conventions
y = 100

#create lists for SLR rate and land cover outputs
slrList = []
marshList = []

while x < 3.1:
    #create a variable for each SLAMM output raster
    slammRaster = r'slammOutputs\dem_OUT, 2100, ' + str(x) + ' meters _GIS.ASC'
    #convert the raster layer to a feature class
    slammPoly = arcpy.conversion.RasterToPolygon(slammRaster, r'output.gdb\marshPolySLRrate_' + str(y), "SIMPLIFY", "Value", "SINGLE_OUTER_PART", None)
    #calculate the area of each land category
    arcpy.management.AddGeometryAttributes(slammPoly, "AREA", '', "SQUARE_KILOMETERS", None)
    #run summary statistics on the feature class
    arcpy.analysis.Statistics(slammPoly, r'output.gdb\summStats_SLRrate_' + str(y), "POLY_AREA SUM", "gridcode")
    slrList.append(x)
    #edit our numeric variables
    x = x + 0.05
    x = round(x, 2)
    if x == 2.0:
        x = int(x)
    else:
        x = x
    if x == 3.0:
        x = int(x)
    else:
        x = x
    y = y + 5
 
#calculate the total area of mearshland in each SLR scenario

for i in range(100, 305, 5):
    stats = 'summStats_SLRrate_' + str(i)
    arcpy.management.SelectLayerByAttribute(stats, "NEW_SELECTION", "GRIDCODE = 8", None)
    with arcpy.da.SearchCursor(stats, ['SUM_POLY_AREA']) as cursor:
        for row in cursor:
            marshList.append(row[0])

#calculate loss

#caluclate the difference between original amount of marshland and amount for each SLR scenario
import numpy
marshAfterSLR = numpy.array(marshList)
difference = originalMarsh - marshAfterSLR
#variable just for printing purposes, not required
z = 1
for i in difference:
    a = round(i, 5)
    print('The loss of marshland for a SLR rate of ' + str(z) + ' meters by 2100 is ' + str(a) + ' square kilometers.')
    z = z + 0.05
    z = round(z, 2)

#export marshland loss calculates to an excel file

import pandas as pd

dict = {'SeaLevelRiseRate': slrList, 'MarshLoss': difference}

df = pd.DataFrame(dict)

df.to_excel(r'path\marshlandLoss.xlsx')

#publish layers (if desired)
#publish desired layers

from IPython.display import display
from arcgis.gis import GIS
import os
gis = GIS('Home')

#variable to identify what layer needs to be converted
x = 100
#convert each scenario feature class to a shapefile for publishing
while x < 301:
    with arcpy.EnvManager(scratchWorkspace=r"\dataPostProcessing\dataPostProcessing.gdb", outputCoordinateSystem="PROJCS['WGS_1984_World_Mercator',GEOGCS['GCS_WGS_1984',DATUM['D_WGS_1984',SPHEROID['WGS_1984',6378137.0,298.257223563]],PRIMEM['Greenwich',0.0],UNIT['Degree',0.0174532925199433]],PROJECTION['Mercator'],PARAMETER['False_Easting',0.0],PARAMETER['False_Northing',0.0],PARAMETER['Central_Meridian',0.0],PARAMETER['Standard_Parallel_1',0.0],UNIT['Meter',1.0]]", workspace=r"\dataPostProcessing\dataPostProcessing.gdb"):
        arcpy.conversion.FeatureClassToShapefile(r"output.gdb\marshPolySLRrate_" + str(x), r"path\publishingShapefiles")
    x = x + 50
    
#before running next block of code, zip the shapefiles in their file locations

a = r'path\publishingShapefiles\100.zip'
b = r'path\publishingShapefiles\150.zip'
c = r'path\publishingShapefiles\200.zip'
d = r'path\publishingShapefiles\250.zip'
e = r'path\publishingShapefiles\300.zip'

shpfileA = gis.content.add({'title':'chsScenario1meter', 'extent':'1442069.5572855,1167159.6686969,1543289.5572855,1260639.6686969'}, data=a)
shpfileB = gis.content.add({'title':'chsScenario1.5meters', 'extent':'1442069.5572855,1167159.6686969,1543289.5572855,1260639.6686969'}, data=b)
shpfileC = gis.content.add({'title':'chsScenario2meters', 'extent':'1442069.5572855,1167159.6686969,1543289.5572855,1260639.6686969'}, data=c)
shpfileD = gis.content.add({'title':'chsScenario2.5meters', 'extent':'1442069.5572855,1167159.6686969,1543289.5572855,1260639.6686969'}, data=d)
shpfileE = gis.content.add({'title':'chsScenario3meters', 'extent':'1442069.5572855,1167159.6686969,1543289.5572855,1260639.6686969'}, data=e)

published_serviceA = shpfileA.publish()
published_serviceB = shpfileB.publish()
published_serviceC = shpfileC.publish()
published_serviceC = shpfileD.publish()
published_serviceC = shpfileE.publish()

display(published_serviceA)
