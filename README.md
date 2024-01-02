# NSGI coordinate reference system transformation examples

The [NSGI](https://www.nsgi.nl/) is responsible for the CRS (coordinate reference system) of the Netherlands and their relations towards international CRS. The NSGI also gives advices and guidance about the usage of these CRS. A large part of these are documented in the Guidance document (in: Dutch) ["Handreiking Gebruik coordinaatreferentiesystemen bij uitwisseling en visualisatie van geo-informatie"](https://docs.geostandaarden.nl/crs/crs/) and the [EPSG repository](https://epsg.org/home.html).

## Goal

The goal of this github organisation and the repositories associated with it is to give a technical perspective into how to implement these advices and guidlines.

## Relations

The image below shows the relevant relations that are there between the Dutch CRS and the international CRS. For the most accurate transformation of coordinates that are with the Netherlands from a Dutch CRS to a international CRS (and vica versa). Defining these relations by making specific transformation rules within proj.db enables users to transform coordinates within the bounds of the Netherlands in a more accurate way.

![relations](supported-transformation-nsgi.drawio.svg)

## Products

![products](products.drawio.svg)

### :earth_africa: proj.db

### :globe_with_meridians: pyproj

### :mag: geodense

### :computer: coordinate transformation

### :whale2: docker

### :tram: coordinate tranformation API
