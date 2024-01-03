# NSGI coordinate reference system transformation examples

The [NSGI](https://www.nsgi.nl/) is responsible for the CRS (coordinate reference system) of the Netherlands and their relations towards international CRS. The NSGI also gives advices and guidance about the usage of these CRS. A large part of these are documented in the Guidance document (in: Dutch) ["Handreiking Gebruik coordinaatreferentiesystemen bij uitwisseling en visualisatie van geo-informatie"](https://docs.geostandaarden.nl/crs/crs/) and the [EPSG repository](https://epsg.org/home.html).

## Goal

The goal of this github organisation and the repositories associated with it is to give a technical perspective into how to implement these advices and guidlines.

## Relations

The image below shows the relevant relations that are there between the Dutch CRS and the international CRS. For the most accurate transformation of coordinates that are with the Netherlands from a Dutch CRS to a international CRS (and vica versa). Defining these relations by making specific transformation rules within proj.db enables users to transform coordinates within the bounds of the European Netherland including the Exclusive Economic Zones in a more accurate way.

![relations](supported-transformation-nsgi.drawio.svg)

## Products

The products that are available, within the context of CRS and their relations, are layered on to of each other. At the core of it being the modified [proj.db](https://proj.org) and the deployed [coordinate transformation API](https://api.transformation.nsgi.nl/v2/) as the "final" product.

Working with CRS and implementing these can be tricky. By showing this layering of different technical solutions, in the image below, we hope to provid a clear breakdown of the relations between the produts that are available. It is also important to note that these products can be used on their on and that this layering can be used as a example how we used our products for deploying the [coordinate transformation API](https://api.transformation.nsgi.nl/v2/).

![products](products.drawio.svg)

### :earth_africa: proj.db

The proj.db is a technical representation of the epsg dataset. The proj.db is used by proj to  implements these defined operations making it possible to transform coordinates between CRS. This makes the proj.db the first technical component available that can be modified. Given this aspect of the proj.db is also makes is in other technical solutions the core component regarding CRS and transformations (i.e [QGIS](https://qgis.org/), [Mapserver](https://mapserver.org/), etc.). If one would also like to altered behaviour of these applications so they use our modified CRS then updating the proj.db would be the prefered technical solution in this case.

> **example**
>
> ```bash
> curl -L -H "Accept: application/octet-stream" https://github.com/GeodetischeInfrastructuur/transformations/releases/download/1.0.0/proj.db -o proj.db
> ```

The modified proj.db is available to download through github.com as:

1. [Dockerfile](https://github.com/GeodetischeInfrastructuur/transformations/blob/main/Dockerfile)
1. [Docker image](https://github.com/GeodetischeInfrastructuur/transformations/pkgs/container/transformations)
1. [Release download](https://github.com/GeodetischeInfrastructuur/transformations/releases)

### :mag: geodense

Geodense is used to check density and densify linestring and polygon geometries. In other words this tool can be used to check 'straightness' of a line and introduce new points to compensated for possible defiations that might occure when such a geometry is transformed from a carthografic to a geographic projection (and vice versa). It preprocesses certain geometry types so they are better suited for transformation.

> :exclamation: For context a linestring in EPSG:28992 that would have a length of over 2km could have a defiation of 5mm. With a line of 20km this can be 26cm. [[1]](https://gnss-data.kadaster.nl/misc/docs/langelijnenadvies.pdf)

For a example how to implement geodense one can look at [coordinate-transformation-api](https://github.com/GeodetischeInfrastructuur/coordinate-transformation-api).

> **example**
>
> ```bash
> pip install geodense
> ```
>
> ```python
> from geodense.lib import check_density_geometry_coordinates
> from geodense.models import DenseConfig
> from geodense.types import Nested, ReportLineString
> from geojson_pydantic import Feature
> from pyproj import CRS
> 
> feature: Feature = {"type": "Feature","properties": {},"geometry": {"type": "LineString","coordinates": [[156264.906359842570964,601302.588919493253343],[165681.> 964475793502061,605544.313164469087496]]}}
> c = DenseConfig(CRS.from_epsg(28992))
> result: Nested[ReportLineString] = check_density_geometry_coordinates(feature['geometry']['coordinates'], c)
> print(result)
> ```

Geodense is available as:

1. [Code](https://github.com/GeodetischeInfrastructuur/geodense)
1. [Pypi package](https://pypi.org/project/geodense/0.0.1a9/)

### :computer: coordinate transformation api

> :warning: The coordiante transformation api makes use of pyproj. Pyproj has it's own proj 'buildin' that needs to be update. By default this one can be found `/usr/local/lib/python3.11/site-packages/pyproj/proj_dir/share/proj/proj.db`.

The [coordinate-transformation-api](https://github.com/GeodetischeInfrastructuur/coordinate-transformation-api) is written in python where the modified proj.db and geodense are used together with specific code to create a API that will transform certain CRS and is focussed on the European Netherland including the Exclusive Economic Zones.

> :warning: The coordinate tranformation api offers transformations that are not possible purely through the use of proj.

The code for the coordinate transformation api is available at:

1. [github.com](https://github.com/GeodetischeInfrastructuur/coordinate-transformation-api)
1. [Dockerfile](https://github.com/GeodetischeInfrastructuur/coordinate-transformation-api/blob/main/Dockerfile)

### :whale2: docker

A Docker image to run your own coordinate transformation api, as a backend service, is also available at github.com. Running the coordinate transformation api in your own container environment eliminates risks by giving the user full controle over the availability of this API.

1. [Docker image](https://github.com/GeodetischeInfrastructuur/coordinate-transformation-api/pkgs/container/coordinate-transformation-api)

### :globe_with_meridians: coordinate tranformation API

For demo purposes we also host this coordinate transformation API on the following endpoint <https://api.transformation.nsgi.nl/v2/>. This can be used for none production critical application and sould no be depended on for high availability.

> **example**
>
> :exclamation: For instance don't use this API in a test environment. The API will go down for maintenance, will throttle under 'high' load, and so on. This will impact the "test" results.
>
> :heavy_check_mark: For implementing these CRS and transformation in a test environment:
>
> 1. spin up a container with the API
> 1. implement the modified proj.db in the used PROJ environment

1. [OpenAPI Specification](https://api.transformation.nsgi.nl/v2/openapi?f=html)
