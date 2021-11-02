There are two important spatial representations of Census geographies we work with at CORI/RISI.

### TIGER/Line

The TIGER/Line files are the most accurate spatial representations of Census geographies. These files should be used for any spatial data analysis. However, the polygons in these files are not suitable for mapping, as they occasionally overlap bodies of water and otherwise look strange when mapped.

__Schema:__ `sch_census_tiger`
__Source Table Format:__  source_tiger_{year}_{geography}
__Layer Table Format:__ tiger_{year}_{geography}
__Current Year:__ 2019

### Cartographic Boundary Files

Also published by the Census, cartographic boundary files are significantly better for visual applications. However, __cartographic boundary files are not suitable for spatial analysis__ and should never be used for such. These files should be applied as a final geometry before exporting to a mapping application.

__Schema:__ `sch_census_tiger`
__Source Table Format:__  source_cb_{year}_{geography}
__Current Year:__ 2019