---
title: "Working with geopackages in R"
layout: post
date: 2018-10-26 22:44
image: http://www.opengeospatial.org/pub/www/files/blog/Geopackage_layers.png
headerImage: true
tag:
- geopackage
- R
star: false
category: blog
author: oldiya
---

## Summary:

- [What is a geopackage](#What-is-a-geopackage?)

- [Using a geopackage file in R](#Using-a-geopackage-file-in-R)

---

## What is a geopackage? 

A [geopackage](https://www.geopackage.org/)[^1] (*.gpkg) is an open format for geospatial information, platform-independent, implemented as a SQLite database. It contains vector features, tile matrix sets of imagery and raster maps at various scales, schema and metadata. Everything is collected in a single file ready to be use, facilitating transfer and usability of the information. 

---

## Using a geopackage file in R

In order to read geopackages in R we are going to use two libraries: rgdal and RSQLite. I am going to exaplin my approach using as an example forestry data from the Finnish center [Metsäkeskus](https://www.metsaan.fi/paikkatietoaineistot)[^2], this data is available [here](ftp://ftp.aineistot.metsaan.fi/Metsavarakuviot/Karttalehti/MV_P4441E.zip)[^3], corresponding with one mapsheet called *MV_P4441E.gpkg*. If you want to follow my example with the same data, please go ahead and download and unzip the file. If you are curius about the forestry data cointaned here you can explore the variables and lear more about the databases in [here](https://extra.bitcomp.fi/metsastandardi_ehdotus/V8/MV/doc/index.html)[^4]. 

When we open a geopackages file  a good first step is to explore the layers it contain: 

```R
  library (rgdal)

  # Explore the layers available 
  ogrListLayers("Data/MV_P4441E.gpkg")
```

In this case we can see that there are 14 layers: 

```R
 [1] "stand"            "study_area_grid"  "stand_dissolved" 
 [4] "stand_clipped"    "stand_grid"       "restriction"     
 [7] "treestratum"      "treestandsummary" "operation"       
[10] "assortment"       "specialfeature"   "datasource"      
[13] "treestand"        "specification"  
attr(,"driver")
[1] "GPKG"
attr(,"nlayers")
[1] 14
```

If I am interested in loading only one of the layers, for example in the layer "stratum " I can do so by using dplyr semantics to directly query the gpkg file:

```R
dta <- src_sqlite ("Data/MV_P4441E.gpkg") 
tbldata <- tbl (dta, "stratum") #Create a table from a data source
tbldf <- as.data.frame (tbldata) #Create a data frame
```

I also created a super simple function to save time, it looks like this:

```R
load_databasegpkg <- function (GPKG, layer){ # e.g.GPKG = "MV_N5411E.gpkg",  layer = "stratum"
  GPKGpath <- paste0("Data/", GPKG)
  dta <- src_sqlite (GPKGpath)
  tbldata <- tbl (dta, layer)
  tbldf <- as.data.frame (tbldata)
  return(tbldf)
}
```

Thats all for now about the gpkg-R connection.

### Links

[^1]: <https://www.geopackage.org/>
[^2]: https://www.metsaan.fi/paikkatietoaineistot
[^3]: ftp://ftp.aineistot.metsaan.fi/Metsavarakuviot/Karttalehti/MV_P4441E.zip
[^4]: https://extra.bitcomp.fi/metsastandardi_ehdotus/V8/MV/doc/index.html





