---
title: "Meet some popular open geospatial standards!"
description: "A short introduction to the powerful GeoPackage, GeoJSON and GeoTIFF standards"
authors: [florisvdh]
date: 2020-02-03
csl: '/tmp/RtmpsgOF7D/research-institute-for-nature-and-forest.csl'
bibliography: ../reproducible_research.bib
categories: ["r", "gis"]
tags: ["gis", "r", "open science"]
output: 
    md_document:
        preserve_yaml: true
        variant: gfm
---

Some inspiration for this post came from the beautiful books of Lovelace
*et al.* (2020), Pebesma & Bivand (2019) and Hijmans (2019), and from
various websites.

## Why use open standards?

  - Open file standards ease collaboration, portability and
    compatibility between users, machines and applications.
  - Their (file) structure is fully documented.
      - Consequently, scientists and programmers can build new software
        / packages and make innovations that use these standards, while
        maintaining interoperability with existing applications.
      - And, there is a much higher chance that your data will still be
        readable in a hundred years from now. The standard’s open
        documentation makes it relatively easy to build tools that can
        read an ancient open-standard file\!

Luckily, quite a list of open standards is available\! Below, some
powerful and widely-used single-file formats are introduced. Single-file
data sources are readily amenable to exchange and publication.

I see you can’t wait to start practicing, so you can also head straight
over to the [tutorial on vector
formats](../../tutorials/spatial_standards_vector/) and the [tutorial on
raster formats](../../tutorials/spatial_standards_raster/)\! In these
tutorials, a comparison table of vector/raster file formats is also
presented.

## A few words on the GDAL library

**[GDAL](https://gdal.org)** (Geospatial Data Abstraction Library) is by
far the most used collection of open-source drivers for:

  - a lot of [raster](https://gdal.org/drivers/raster/index.html)
    formats;
  - a lot of [vector](https://gdal.org/drivers/vector/index.html)
    formats.

In other words, it is the preferred workhorse for reading and writing
many geospatial file formats, used in the background by a lot of
[geospatial
applications](https://gdal.org/software_using_gdal.html#software-using-gdal)
. Using GDAL is the easiest way to conform to open standards.

So, in R we use packages that use GDAL in the background, such as
`rgdal`, `sp`, `sf`, `raster` and `stars`.

## The GeoPackage file format

  - Its website is <https://www.geopackage.org>.
  - It is a standardized implementation of an SQLite database for
    geospatial data. Hence, a GeoPackage is a **binary** file
    (`filename.gpkg`). It shares this property with shapefiles, which
    however pose multiple limitations,\[1\] so the GeoPackage is a more
    than suitable replacement.
  - The GeoPackage can store one or *multiple* **vector** layers
    (points, lines, polygons and related feature types). Besides vector
    data, it can also store **raster** data or extra standalone
    **tables**. These properties make it somehow comparable to the
    ‘personal geodatabase’ of ArcGIS – ESRI’s closed, Windows-only
    format.\[2\]
  - The GeoPackage standard is
    [maintained](https://www.opengeospatial.org/standards/geopackage) by
    the [Open Geospatial Consortium](https://www.opengeospatial.org/)
    (OGC), which stands out as a reference when it comes to open
    geospatial standards.

## The GeoJSON file format

  - One [GeoJSON](https://tools.ietf.org/html/rfc7946) file
    (`filename.geojson`) contains *one* **vector** layer. Note that one
    vector layer can combine different [feature geometry
    types](https://r-spatial.github.io/sf/articles/sf1.html#simple-feature-geometry-types),
    e.g. points and linestrings.
    [JSON](https://en.wikipedia.org/wiki/JSON) itself is a common and
    straightforward open data format. It is a **text** file readable
    both by humans and machines (see the
    [tutorial](../../tutorials/spatial_standards_vector/) for an
    example). GeoJSON adds the necessary specification to JSON for
    standardized storage of geographic feature data, but it is still a
    plain JSON text file.
  - The GeoJSON standard is maintained by the Internet Engineering Task
    Force ([IETF](https://www.ietf.org/)), a large open standards
    organization that develops Internet standards under the auspices of
    the Internet Society.
  - Although the previous version of the GeoJSON standard – GeoJSON 2008
    – is still a lot in use, it is
    [obsoleted](http://geojson.org/geojson-spec.html) and a new version
    **[RFC7946](https://tools.ietf.org/html/rfc7946)** is establishing.
      - This version is strict about the coordinate reference system
        (CRS) – it is always [WGS84](https://epsg.io/4326) – and it also
        differs on a few other aspects (such as the recommendation for
        applications [not to
        inflate](https://tools.ietf.org/html/rfc7946#section-11.2)
        decimal coordinate precision).
      - RFC7946 solves the problem that quite a few libraries –
        including GDAL – simply assumed WGS84 in GeoJSON 2008 (without
        checking or transforming), even though WGS84 was not a
        requirement of GeoJSON 2008 (it did support an explicit *crs*
        declaration). This resulted in inconveniences (cf. [this
        post](https://github.com/r-spatial/sf/issues/344#issue-229118527)
        in the `sf`-repository).
      - A [specific
        section](https://gdal.org/drivers/vector/geojson.html#rfc-7946-write-support)
        in the documentation of GDAL’s GeoJSON driver gives a summary of
        the differences between both GeoJSON versions.
  - While GDAL by default still follows the GeoJSON 2008 format,\[3\]
    RFC7946 is supported by the option `RFC7946=YES`. Here, reprojection
    to WGS84 will happen automatically. It applies 7 decimal places for
    coordinates, i.e. approximately 1 cm. Given the advantages, ***we
    advise to explicitly use RFC7946***. Several functions in R allow
    the user to provide options that are passed to GDAL, so we can ask
    to deliver RFC7946 (see the
    [tutorial](../../tutorials/spatial_standards_vector/)).
  - In order to keep it manageable (text file size, usage in versioning
    systems\[4\] ) it can be wise to use GeoJSON for more simple cases
    (points and rather simple lines and polygons), and use the binary
    GeoPackage format for larger (more complex) cases.

## The GeoTIFF file format

  - [GeoTIFF](https://en.wikipedia.org/wiki/GeoTIFF) is the preferred
    single-file open standard for **raster** data. It adheres to the
    open [TIFF](https://en.wikipedia.org/wiki/TIFF) specification; hence
    it is a TIFF image file (`filename.tif`). It
    [uses](http://docs.opengeospatial.org/is/19-008r4/19-008r4.html#_geotiff_file_structure_and_geotiff_crs_and_models_principles_informative)
    a small set of reserved TIFF tags to store information about CRS,
    extent and resolution of the raster.
  - A GeoTIFF file can contain *one* or *multiple* rasters with the same
    CRS, extent and resolution.
  - The GeoTIFF standard is
    [maintained](https://www.opengeospatial.org/standards/geotiff) by
    the [Open Geospatial Consortium](https://www.opengeospatial.org/)
    (OGC), which stands out as a reference when it comes to open
    geospatial standards.

## Literature

<div id="refs" class="references">

<div id="ref-heijmans_spatial_2019">

Hijmans R. (2019). Spatial Data Science with R. <https://rspatial.org/>.

</div>

<div id="ref-lovelace_geocomputation_2020">

Lovelace R., Nowosad J. & Muenchow J. (2020). Geocomputation with R.
<https://geocompr.robinlovelace.net>.

</div>

<div id="ref-pebesma_edzer_spatial_2019">

Pebesma E. & Bivand R. (2019). Spatial Data Science.
<https://www.r-spatial.org/book>.

</div>

</div>

1.   Some problems with shapefiles are: they’re not an open format, they
    consist of multiple files and they have restrictions regarding file
    size, column name length, number of columns and the feature types
    that can be accommodated.

2.   Note that personal geodatabases have their size limited to 250-500
    MB; a GeoPackage can have a size of about 140 TB if the filesystem
    can handle it.

3.   Though GeoJSON 2008 is obsoleted, the now recommended RFC7946
    standard is still officially in a *proposal* stage. That is probably
    the reason why GDAL does not yet default to RFC7946. A somehow
    confusing stage, it seems.

4.   When versioning GeoJSON files, mind the order of your data when
    rewriting them: reordering could produce large diffs. Interested in
    combining GeoJSON and GitHub? [Surprise
    yourself](https://github.com/lyzidiamond/learn-geojson)\!
