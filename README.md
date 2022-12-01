# Mountain Identification
This is a repository for my final project (matching mountain ridgelines to DEM generate viewsheds) for class Programming GIS 5578 (taken Fall of 2022 at the U of M).)

## The Big Picture
This project is part of a 'bigger picture'. The End objective is to identify a mountain from an image, given that the image's metadata contains its GPS coordinates. This objective is broken down into general following steps:

1. Automatically delineate the mountain ridgeline from an image (please see [Mountain-Image-Classification](https://github.com/mmarsole/Mountain-Image-Classification) for previous work on implementing semantic segmentation to delineate Mountains from imagery).  

2. Based on the GPS coordinates associated for each image, generate a 360-degree view ridgeline (this is the expected ridgeline one should see based solely on Digital Elevation Models (DEMs) and does not consider object that could interfere with an observer's view at the designated location for example trees or buildings).

3. Match the delineated ridgeline extracted from the image (see step 1) to the ridgeline generated in step 2 (this involves rescaling the image's ridgeline to generally match the 360-degree ridgeline and then perform interactive test to find the area that the image ridgeline best fits with the 360-degree ridgeline)

4. Once the image ridgeline has been matched to the 360-degree ridgeline, identify the name of the Mountain, based on a spatial query (presumably, once the image's ridgeline is matched to the 360 degree ridgeline, we can located where the 'highest' (maxima) peaks in each of the image’s ridgeline are located based on their match to the 360 degree ridgeline). 

The code in this repository focuses on steps 2 and 3. 

## Code Methodology 
Here, you can learn read a more detailed explanation of the process performed for generating a 360-degree view ridgeline. 

1. Extract the GPS data for a given Image. 

Not every image has GPS metadata but if a given image does contain the relevant metadata, the "MetaData-GPS.ipynb" file will extract the GPS metadata for JPG and HEIC images and save it as a csv. 

2. Acquire relevant DEM file (the DEM file was manually extracted from [USGS Earth Explorer](https://earthexplorer.usgs.gov/)).

3. Run "Viewsheds.ipynb" file. 

This file imports the GPS csv generated from "MetaData-GPS.ipynb". It then proceeds to create a shapefile for each image's location that is used to calculate the [viewshed](https://pro.arcgis.com/en/pro-app/latest/tool-reference/spatial-analyst/viewshed.htm) ("a computer-generated map or model of the view of an area from a specific vantage point"). With this viewshed we can mask the DEM raster to retain only pixels (and their elevations) that are visible to the observer at each provided location (the image's location). 

Polylines are created radiating from each image location based on points plotted evenly around the minimum bounding geometry that contains the visible viewshed output. These polylines are labeled and identified based on their respective 'Degree' (0 to 360) where North is 0 and polylines degree increase clockwise. Based on spatial Join between these polylines and the visible pixels (a.k.a. visible elevation). For each Polyline, the max elevation pixel is retained, thus enabling us to identify the max elevation at each degree interval in a 360 view. Next we extract the distance (in km) from each max elevation pixel to the image location by clipping the polylines based on their intersection with each 'max' elevation pixel. 

Here's a visualization of the steps executed with "Viewsheds.ipynb":

![generating Ridgeline](/readme_sup_docs/Generating_Ridgeline.JPG)

4. Plotting the 360-degree ridgeline. 

Run "Ridgline_Visuals.ipynb", which plots the ridgeline based on the following equation: h/l = H/L where h is perceived height of an object, l the distance from the 'observer' to the measuring instrument, H is the actual height of the object, and L the distance of the observer from the object. 

Based on this perspective ratio, we can calculate the 'perceived height' of the mountain (h) in a 360-degree view around the observer. Thus, creating a ridgeline that should be visible for each provided location.  
 
