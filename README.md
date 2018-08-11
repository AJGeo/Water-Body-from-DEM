 # Water Body from DEM

 ## Back Ground
 This came to be out of necessity and curiosity.
 I wanted water bodies on Topo or any other maps for that matter.
 See DEM 2 Topo for context

 ## Tools:
	- DGal (www.gdal.org)
	- PostGIS
	- Notepad++
	- Spreadsheet
	- Operating system scrip (ie. bat)

 ## TIP:
	- Write the output to a dedicated folder.
	- Add spacial index at the end, you will save a lot of time.
	- Examples assumes the use of projection WGS84. If you use something else, adjust accordingly.

 ## Process

 ######Create Slope from DEM
 gdaldem slope C:\GIS\STRM\AfriS\s01_e037_1arc_v3.bil C:\GIS\STRM\AfriS\Slope\s01_e037_slope.tif -s 111120 -compute_edges -of GTiff
 Value range 0 to 90 degrees
 Reference the GDal documentation on slope

 ######Make Polygons from slope
 gdal_polygonize.bat C:/GIS/STRM/AfriS/Slope/s01_e037_slope.tif -f "ESRI Shapefile" C:/GIS/STRM/AfriS/Slope/WaterArias/s01_e037_water.shp s01_e037_water
 This created a polygon shape file from your slope file.

 ######Import into PostGIS
 Use the PostGIS tool. Remember to set the projection WGS84(EPSG:4326)
 Check PostGIS documentation
 
 ######Delete unwanted records
 Run Query's to delete all records with value not 0

 ######Add area column
 Add a area column to your table of value double

 ######Calculate aria
 Calculate square meter of polygons
 UPDATE [table] SET area = ST_Area(geography(geom));
 Check PostGIS documentation
 
 ######Delete small arias
 Delete all records with area smaller than 3hec (30000)

 ######Make DB smaller
 [VACUUM FULL ANALIZE] table

 ######Add spacial index
 Time to spacial index your tables.
 Check PostGIS documentation
 
 ## Optional
 gdal_rasterize