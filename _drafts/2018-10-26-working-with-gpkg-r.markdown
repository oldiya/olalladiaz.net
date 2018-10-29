---
title: "Working with geopackages in R"
layout: post
date: 2018-10-26 22:44
image: /assets/images/markdown.jpg
headerImage: false
tag:
- geopackage
- R
star: false
category: blog
author: Olalla Díaz-Yáñez
description: Markdown summary with different options
---

## Summary:

- [What is a geopackage](#What-is-a-geopackage?)

- [Using a geopackage file in R](#Using-a-geopackage-file-in-R)

---

## What is a geopackage? 

A [geopackage](https://www.geopackage.org/)[^1] (*.gpkg) is an open format for geospatial information, platform-independent, implemented as a SQLite database containing vector features, tile matrix sets of imagery and raster maps at various scales, schema and metadata. Everything is contained in a single file ready to be use, facilitating transfer and usability of the information. 

---

## Using a geopackage file in R

In order to be able to read geopackages in R we are goign to use tow main libraries: rgdal and RSQLite. Here there is an example using a forestry data from the Finnish center [Metsäkeskus](https://www.metsaan.fi/paikkatietoaineistot)[^2], the data I am using in the example is available [here](ftp://ftp.aineistot.metsaan.fi/Metsavarakuviot/Karttalehti/MV_P4441E.zip)[^3] which coresponsd with one mapsheet called *MV_P4441E.gpkg*. If you want to follow, please download the file and unzip it (I had to use Unarchiver.app as I could not open it with ), if you are curius about the forestry data cointaned here you can explore the variables in [here](https://extra.bitcomp.fi/metsastandardi_ehdotus/V8/MV/doc/index.html)[^4]. 

A good first step is to explore the layers contained in the geopackage file: 



```R
  library (rgdal)
  
  # Explore the layers available 
  ogrListLayers("Data/MV_P4441E.gpkg")
```


In this case we can see that there are 14 layers 

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

If I am interested in only one of the layers, for example in the layer "stratum " I can load using dplyr semantics to directly query the gpkg file:

```R
dta <- src_sqlite ("Data/MV_P4441E.gpkg") 
tbldata <- tbl (dta, "stratum") #Create a table from a data source
tbldf <- as.data.frame (tbldata)
```

I also created a super simple function to save time for each time that I want to load a different layer in each package, it looks like this:

```R
load_databasegpkg <- function (GPKG, layer){ # e.g.GPKGpath <- "MV_N5411E.gpkg",  layer <- "stratum"
  GPKGpath <- paste0("Data/", GPKG)
  dta <- src_sqlite (GPKGpath)
  tbldata <- tbl (dta, layer)
  tbldf <- as.data.frame (tbldata)
  return(tbldf)
}
```

Thats all for now about the gpkg-R connection.



[^1]: https://www.geopackage.org/
[^2]: https://www.metsaan.fi/paikkatietoaineistot
[^3]: ftp://ftp.aineistot.metsaan.fi/Metsavarakuviot/Karttalehti/MV_P4441E.zip
[^4]: https://extra.bitcomp.fi/metsastandardi_ehdotus/V8/MV/doc/index.html





