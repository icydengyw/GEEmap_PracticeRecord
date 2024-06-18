Here, we show the example of exporting the water occurrence image from the JRC GSW data set to local storage.

![image](https://github.com/icydengyw/GEEmap_PracticeRecord/assets/48988534/4f7b1f05-c1a0-44ee-bbbf-d1d630d54017)
![image](https://github.com/icydengyw/GEEmap_PracticeRecord/assets/48988534/84bffc00-a171-4136-abba-42d2b84fe5e6)

```python
import geemap
import ee

# Load JRC Global Surface Water Mapping Layers datset
gsw = ee.Image('JRC/GSW1_4/GlobalSurfaceWater')

# Load region from Earth Engine Asset
region = ee.FeatureCollection("projects/ee-ciscodengyw/assets/colombia_main")

# Derive Water Occurrence image
occurrence = gsw.select('occurrence')

# Define export local path
output_file = r'A:\xxx\DATA\occurrence_image.tif'

# Export Water Occurrence image to local path
geemap.ee_export_image(occurrence, filename=output_file, scale=30, region=region.geometry(), file_per_band=False)

```
output:
Generating URL ...
An error occurred while downloading.
Pixel grid dimensions (55294x65403) must be less than or equal to 32768.

If the region is small, this code is okay. But if the region is too large, like 18 degrees multiply 25 degrees, then you could export by tiles.

```python
import geemap
import ee
import math

# Initialize Earth Engine
ee.Initialize()

# Load JRC Global Surface Water Mapping Layers dataset
gsw = ee.Image('JRC/GSW1_4/GlobalSurfaceWater')

# Define local path
locapath = r'A:\xxx\DATA\JRCwater\\'

# Load study region from Earth Engine asset
region = ee.FeatureCollection("projects/ee-ciscodengyw/assets/colombia_main")

# Select the 'occurrence' band from the dataset
occurrence = gsw.select('occurrence').clip(region)

# Define the size of each grid cell (in degrees)
grid_size = 1.0

# Get the bounding box of the study region
region_bounds = region.geometry().bounds().getInfo()['coordinates'][0]
min_lon = min([point[0] for point in region_bounds])
max_lon = max([point[0] for point in region_bounds])
min_lat = min([point[1] for point in region_bounds])
max_lat = max([point[1] for point in region_bounds])

# Calculate the number of grid cells along longitude and latitude
lon_steps = math.ceil((max_lon - min_lon) / grid_size)
lat_steps = math.ceil((max_lat - min_lat) / grid_size)
print("lon_steps: ",lon_steps)
print("lat_steps: ",lat_steps)
# Create grid cells and export images
for i in range(lon_steps):
    for j in range(lat_steps):
        lon_min = min_lon + i * grid_size
        lon_max = min(lon_min + grid_size, max_lon)
        lat_min = min_lat + j * grid_size
        lat_max = min(lat_min + grid_size, max_lat)
        
        grid = ee.Geometry.Rectangle([lon_min, lat_min, lon_max, lat_max])
        
        # Define output file name for each grid cell
        output_file = f'{locapath}occurrence_image_{i}_{j}.tif'
        try:
            # Export the occurrence image for the current grid cell
            geemap.ee_export_image(
                occurrence,
                filename=output_file,
                scale=30,
                region=grid,
                file_per_band=False
            )
            print(f"The occurrence image has been downloaded to {output_file}")
        except Exception as e:
            print(f"An error occurred while downloading the grid {i}, {j}: {e}")
```
Advanced: we checked if there is any water pixel exists in the grid and decide whether to export it or not

![image](https://github.com/icydengyw/GEEmap_PracticeRecord/assets/48988534/6a470c3e-5eac-4904-9a6d-3c3eac807b89)
resolved the bug:
```python
import geemap
import ee
import math

# Initialize Earth Engine
ee.Initialize()

# Load JRC Global Surface Water Mapping Layers dataset
gsw = ee.Image('JRC/GSW1_4/GlobalSurfaceWater')

# Define local path
locapath = r'A:\05_Columbia\DATA\JRCwater\\'

# Load study region from Earth Engine asset
region = ee.FeatureCollection("projects/ee-ciscodengyw/assets/colombia_main")

# Select the 'occurrence' and 'max_extent' bands from the dataset
occurrence = gsw.select('occurrence').clip(region)
max_extent = gsw.select('max_extent').clip(region)

# Define the size of each grid cell (in degrees)
grid_size = 1.0

# Get the bounding box of the study region
region_bounds = region.geometry().bounds().getInfo()['coordinates'][0]
min_lon = min([point[0] for point in region_bounds])
max_lon = max([point[0] for point in region_bounds])
min_lat = min([point[1] for point in region_bounds])
max_lat = max([point[1] for point in region_bounds])

# Calculate the number of grid cells along longitude and latitude
lon_steps = math.ceil((max_lon - min_lon) / grid_size)
lat_steps = math.ceil((max_lat - min_lat) / grid_size)
print("lon_steps: ", lon_steps)
print("lat_steps: ", lat_steps)

# Create grid cells and export images
for i in range(lon_steps):
    for j in range(lat_steps):
        lon_min = min_lon + i * grid_size
        lon_max = min(lon_min + grid_size, max_lon)
        lat_min = min_lat + j * grid_size
        lat_max = min(lat_min + grid_size, max_lat)
        
        grid = ee.Geometry.Rectangle([lon_min, lat_min, lon_max, lat_max])
        
        # Check if the grid contains any water
        water_present = max_extent.reduceRegion(
            reducer=ee.Reducer.max(),
            geometry=grid,
            scale=30,
            maxPixels=1e8,  # Set maxPixels to a higher value
            bestEffort=True  # Set bestEffort to true
        ).get('max_extent').getInfo()
        
        if water_present == 1:
            # Define output file name for each grid cell
            output_file = f'{locapath}occurrence_image_{i}_{j}.tif'
            try:
                # Export the occurrence image for the current grid cell
                geemap.ee_export_image(
                    occurrence,
                    filename=output_file,
                    scale=30,
                    region=grid,
                    file_per_band=False
                )
                print(f"The occurrence image has been downloaded to {output_file}")
            except Exception as e:
                print(f"An error occurred while downloading the grid {i}, {j}: {e}")

```
