
<!-- README.md is generated from README.Rmd. Please edit that file -->

# ptolemy: an R package for accessing global high-resolution geography

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.1470706.svg)](https://doi.org/10.5281/zenodo.1470706)

## About

This package was formerly known as `nPacMaps`. This R package relies on
data downloaded from the [Global Self-consistent, Hierarchical,
High-resolution Geography (GSHHG)
Database](https://www.ngdc.noaa.gov/mgg/shorelines/data/gshhg/latest/).
More details on the GSHHG can be found on this [website from the
University of Hawaii](http://www.soest.hawaii.edu/wessel/gshhg/). The
current version supported in this package is 2.3.7 (July, 2017).

The main objective is to provide a simple interface for loading basemap
polygon land data for the North Pacific region that can be used in the
`sf` and `ggplot2` R package ecosystem. There are a variety of
pre-described regions that are loaded with convenience functions.
However, most regions of the world are supported and custom extractions
are possible with `extract_gshhg()`.

This package is under development and functionality is subject to change
and improvement at anytime.

Please cite this package as:

> London, Josh M. ptolemy: an R package for accessing global
> high-resolution geography. 2018. Version 1.0.0.
> <https://github.com/jmlondon/ptolemy>. 10.5281/zenodo.1470706

and, also cite the GSHHG dataset:

> Wessel, P., and W. H. F. Smith. A Global Self-consistent,
> Hierarchical, High-resolution Shoreline Database. J. Geophys. Res.,
> 101, 8741-8743, 1996.

## Installation

The ptolemy package is not available on CRAN and must be installed via
the `devtools::install_github()` function.

``` r
install.packages("devtools")
devtools::install_github('jmlondon/ptolemy')
```

After successfully installing the package from GitHub, you will need to
download and install the GSHGG data. This is handled via the
`ptolemy::install_gshhg()` function. If you are not prompted, you may
need to run `ptolemy::install_gsggh()` before using the package.

``` r
library(ptolemy)
install_gshhg()
```

## Data for Specifying the Geographic Region

The `data` parameter should be an `sf` object representing the region of
interest. This can be data points, polygons, lines and should be
projected. If the data are provided in longlat, an *epsg* parameter code
is required.

## Resolution

The GSHHG dataset has five different resolutions available:

1.  **f**ull resolution: Original (full) data resolution.
2.  **h**igh resolution: About 80 % reduction in size and quality.
3.  **i**ntermediate resolution: Another ~80 % reduction.
4.  **l**ow resolution: Another ~80 % reduction.
5.  **c**rude resolution: Another ~80 % reduction.

The **i**ntermediate reolustion has been set as the default option and
should suffice for most applications. The default resolution can be
overided via the `resolution` parameter. Users should consider bumping
up to **h**igh or **f**ull when zooming into smaller scale regions. This
will increase the extraction and drawing time. If you require the
**f**ull resolution frequently, creating a custom region via
`extract_gshhg()` should be considered.

## ESPG / Projection

All of the returned maps are provide with projected coordinates based on
the `epsg` parameter provided. For the pre-built regions, the default
projections are sensible and all users need to do is insure all other
data is transformed to the same projection. A custom projection can be
provided.

## Buffer

The returned map region will be slightly larger than the area of
interest represented by the provided data. The default is 5000 meters.

## Simplified for Improved Performance

The `rmapshaper::ms_simplify` function can applied to the returned
objects to improve performance. The function is set to return 20% of the
original points and to preserve small shapes (i.e. small islands and
other features). This improves performance for plotting and should
suffice for most users. However, the original integrity of the GSHHG
data will be changed. Uses for analytical purposes (e.g. calculating
distance to shoreline, least-cost path creation around land) should not
use this option without consideration of impacts.

## geom\_sf for ggplot2

By default all of the basemap objects are returned as an `sf` object.
ggplot2 now offers native support for plotting `sf` objects via the
`geom_sf`. The examples below demonstrate basic use of `geom_sf`.

## Acknowledgements

This package relies heavily on the works of other researchers and
developers. The GSHHG dataset is developed and maintained by Paul Wessel
(SOEST, University of Hawai’i, Honolulu, HI) and Walter H. F. Smith
(NOAA Geosciences Lab, National Ocean Service, Silver Spring, MD). The
package also relies on code developed as part of the `PBSmapping`
package (Jon T. Schnute, Nicholas Boers and Rowan Haigh (2018).
PBSmapping: Mapping Fisheries Data and Spatial Analysis Tools. R package
version 2.70.5. <https://CRAN.R-project.org/package=PBSmapping>.)

## Examples

### North Pacific Basemap

``` r
library(ggplot2)
library(ptolemy)
library(sf)
#> Linking to GEOS 3.6.2, GDAL 2.3.0, PROJ 5.1.0

npac_base <- ptolemy::npac()
#> importGSHHS status:
#> --> Pass 1: complete: 15631 bounding boxes within limits.
#> --> Pass 2: complete.
#> --> Clipping...
#> Warning in refocusWorld(as.PolySet(as.data.frame(xres), projection = "LL"), : Removed duplicates of following polygons (Antarctica?): 0, 1, 15
#> importGSHHS: input xlim was (0, 360) and the longitude range of the extracted data is (0, 359.532917).
#> Warning in st_buffer.sfc(st_geometry(x), dist, nQuadSegs, endCapStyle =
#> endCapStyle, : st_buffer does not correctly buffer longitude/latitude data
#> dist is assumed to be in decimal degrees (arc_degrees).

npac_plot <- ggplot() + 
  geom_sf(data = npac_base,
               fill = "grey60", size = 0.2) +
  ggtitle('North Pacific Basemap (epsg:3832)')
npac_plot
```

![](README-npac-example-1.png)<!-- -->

### California Current

``` r
library(ggplot2)
library(ptolemy)
library(sf)

calcur_base <- ptolemy::calcur()
#> importGSHHS status:
#> --> Pass 1: complete: 5028 bounding boxes within limits.
#> --> Pass 2: complete.
#> --> Clipping...
#> Warning in refocusWorld(as.PolySet(as.data.frame(xres), projection = "LL"), : Removed duplicates of following polygons (Antarctica?): 0, 1, 15
#> importGSHHS: input xlim was (0, 360) and the longitude range of the extracted data is (0, 359.532917).
#> Warning in st_buffer.sfc(st_geometry(x), dist, nQuadSegs, endCapStyle =
#> endCapStyle, : st_buffer does not correctly buffer longitude/latitude data
#> dist is assumed to be in decimal degrees (arc_degrees).

calcur_plot <- ggplot() + 
  geom_sf(data = calcur_base,
               fill = "grey60", size = 0.2) +
  ggtitle('California Current Basemap (epsg:3310)')
calcur_plot
```

![](README-calcur-example-1.png)<!-- -->

### Bering Sea Basemap

``` r
library(ggplot2)
library(ptolemy)
library(sf)

bering_base <- ptolemy::bering()
#> importGSHHS status:
#> --> Pass 1: complete: 13265 bounding boxes within limits.
#> --> Pass 2: complete.
#> --> Clipping...
#> Warning in refocusWorld(as.PolySet(as.data.frame(xres), projection = "LL"), : Removed duplicates of following polygons (Antarctica?): 0, 15
#> importGSHHS: input xlim was (0, 360) and the longitude range of the extracted data is (0, 359.442861).
#> Warning in st_buffer.sfc(st_geometry(x), dist, nQuadSegs, endCapStyle =
#> endCapStyle, : st_buffer does not correctly buffer longitude/latitude data
#> dist is assumed to be in decimal degrees (arc_degrees).

bering_plot <- ggplot() + 
  geom_sf(data = bering_base,
               fill = "grey60", size = 0.2) +
  ggtitle('Bering Sea Basemap (epsg:3571)')
bering_plot
```

![](README-bering-example-1.png)<!-- -->

### US (Alaska) Arctic Basemap

``` r
library(ggplot2)
library(ptolemy)
library(sf)

us_arctic_base <- ptolemy::us_arctic()
#> importGSHHS status:
#> --> Pass 1: complete: 9256 bounding boxes within limits.
#> --> Pass 2: complete.
#> --> Clipping...
#> Warning in refocusWorld(as.PolySet(as.data.frame(xres), projection = "LL"), : Removed duplicates of following polygons (Antarctica?): 0, 15
#> importGSHHS: input xlim was (0, 360) and the longitude range of the extracted data is (4.585778, 359.275833).
#> Warning in st_buffer.sfc(st_geometry(x), dist, nQuadSegs, endCapStyle =
#> endCapStyle, : st_buffer does not correctly buffer longitude/latitude data
#> dist is assumed to be in decimal degrees (arc_degrees).

us_arctic_plot <- ggplot() + 
  geom_sf(data = us_arctic_base,
               fill = "grey60", size = 0.2) +
  ggtitle('US Arctic Basemap (epsg:3572)')
us_arctic_plot
```

![](README-us-arctic-example-1.png)<!-- -->

### Alaska Basemap

``` r
library(ggplot2)
library(ptolemy)
library(sf)

ak_base <- ptolemy::alaska()
#> importGSHHS status:
#> --> Pass 1: complete: 10988 bounding boxes within limits.
#> --> Pass 2: complete.
#> --> Clipping...
#> Warning in refocusWorld(as.PolySet(as.data.frame(xres), projection = "LL"), : Removed duplicates of following polygons (Antarctica?): 0, 15
#> importGSHHS: input xlim was (0, 360) and the longitude range of the extracted data is (0, 359.275833).
#> Warning in st_buffer.sfc(st_geometry(x), dist, nQuadSegs, endCapStyle =
#> endCapStyle, : st_buffer does not correctly buffer longitude/latitude data
#> dist is assumed to be in decimal degrees (arc_degrees).

ak_plot <- ggplot() + 
  geom_sf(data = ak_base,
               fill = "grey60", size = 0.2) +
  ggtitle('Alaska Basemap (epsg:3338)')
ak_plot
```

![](README-ak-example-1.png)<!-- -->

We can also zoom in on a particular region

``` r
library(ggplot2)
library(ptolemy)
library(crawl)
#> crawl 2.2.2 (2018-09-17) 
#>  Demos and documentation can be found at our new GitHub repository:
#>  https://dsjohnson.github.io/crawl_examples/
library(dplyr)
#> 
#> Attaching package: 'dplyr'
#> The following objects are masked from 'package:stats':
#> 
#>     filter, lag
#> The following objects are masked from 'package:base':
#> 
#>     intersect, setdiff, setequal, union
library(sf)

data("harborSeal")

harborSeal <- harborSeal %>% 
  filter(!is.na(latitude)) %>% 
  as.data.frame() %>% 
  sf::st_as_sf(coords = c("longitude","latitude")) %>% 
  sf::st_set_crs(4326) %>% 
  sf::st_transform(3338)


map_base <- ptolemy::extract_gshhg(data = harborSeal)
#> importGSHHS status:
#> --> Pass 1: complete: 2198 bounding boxes within limits.
#> --> Pass 2: complete.
#> --> Clipping...
#> Warning in refocusWorld(as.PolySet(as.data.frame(xres), projection = "LL"), : Removed duplicates of following polygons (Antarctica?): 0, 15
#> importGSHHS: input xlim was (0, 360) and the longitude range of the extracted data is (4.854056, 358.222472).
#> Warning in st_buffer.sfc(st_geometry(x), dist, nQuadSegs, endCapStyle =
#> endCapStyle, : st_buffer does not correctly buffer longitude/latitude data
#> dist is assumed to be in decimal degrees (arc_degrees).

ak_plot <- ggplot() + 
  geom_sf(data = map_base,
               fill = "grey60", size = 0.2) +
  geom_sf(data = harborSeal,
             alpha = 0.1, color = 'blue') 
ak_plot
```

![](README-ak-example-zoom-1.png)<!-- -->

-----

##### Disclaimer

<sub>This repository is a scientific product and is not official
communication of the Alaska Fisheries Science Center, the National
Oceanic and Atmospheric Administration, or the United States Department
of Commerce. All AFSC Marine Mammal Laboratory (AFSC-MML) GitHub project
code is provided on an ‘as is’ basis and the user assumes responsibility
for its use. AFSC-MML has relinquished control of the information and no
longer has responsibility to protect the integrity, confidentiality, or
availability of the information. Any claims against the Department of
Commerce or Department of Commerce bureaus stemming from the use of this
GitHub project will be governed by all applicable Federal law. Any
reference to specific commercial products, processes, or services by
service mark, trademark, manufacturer, or otherwise, does not constitute
or imply their endorsement, recommendation or favoring by the Department
of Commerce. The Department of Commerce seal and logo, or the seal and
logo of a DOC bureau, shall not be used in any manner to imply
endorsement of any commercial product or activity by DOC or the United
States Government.</sub>
