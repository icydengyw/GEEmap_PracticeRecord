# Geemap Learning and Notes

Welcome to the Geemap Learning and Notes repository! This repository is designed for newcomers to geemap, a powerful Python package for interactive mapping with Google Earth Engine (GEE). Here, you'll find a collection of tutorials, example scripts, and notes that will help you get started with geemap and harness its full potential. Let's start!😊

## What is Geemap?

Geemap is a Python package that brings the power of Google Earth Engine to your Jupyter Notebook environment. It provides an intuitive interface for creating, visualizing, and analyzing geospatial data. With geemap, you can easily interact with GEE datasets, perform complex analyses, and create stunning maps.

## Who is this for?

This repository is aimed at beginners who are new to geemap and Google Earth Engine. Whether you're a student, researcher, or GIS professional, you'll find useful resources here to kickstart your geospatial projects.

## Getting Started

### Installation

It is highly recommended that you **create a fresh conda environment to install geemap**. Follow the commands below to set up a conda env and install geemap:

Mamba is a faster, more efficient package manager compared to Conda. Mamba is known for being more reliable in handling dependency conflicts and providing more informative error messages when something goes wrong.
By using mamba install, you can save time and avoid potential headaches associated with slow package installations and complex dependency resolutions.


```bash
# Create and activate a new environment with Python 3.11
conda create -n gee python=3.11
conda activate gee

# Install mamba in the base environment
conda install -n base mamba -c conda-forge

# Use mamba to install the required packages
mamba install geemap -c conda-forge
mamba install geopandas localtileserver -c conda-forge

# Use mamba to install ipykernel
mamba install ipykernel

# Create a new IPython kernel for the gee environment
python -m ipykernel install --name gee

# Use mamba to install jupyter_contrib_nbextensions
mamba install jupyter_contrib_nbextensions -c conda-forge

# Update geemap to the latest version
conda update -c conda-forge geemap

```

Then, you could use geemap in Jupyter notebook!
Remember to authenticate using the following codes for the first time, and you do not need to do it again in the future. I believe you have a GEE account already.
PS: Open in a now tab and copy the verify code and paste it into the box
```python
import ee
#Trigger the authentication flow.
ee.Authenticate()
#Initialize the library.
ee.Initialize()

```
Let's try our first code!
```python
# Create an interactive map
import ee
import geemap
Map = geemap.Map(center=(40, -100), zoom=4)
Map
```

[Examples](docs/Example_1.md)


