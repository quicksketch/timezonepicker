Building new shape files
------------------------

1. Download latest shape file "tz_world" from
   http://efele.net/maps/tz/world/.

   wget http://efele.net/maps/tz/world/tz_world.zip
   unzip tz_world.zip

2. Install PostGIS, which provides the shp2pgsql executable.
   http://postgis.refractions.net/download/

   For Mac OS X, I installed PostGres, GDAL Complete, and PostGIS binaries from
   http://www.kyngchaos.com/software:postgres

   Then add psql and shp2pgsql to your $PATH variable in your shell profile.
   export PATH=/usr/local/pgsql-9.1/bin:$PATH


3. Convert the tz_world.shp file into SQL:

   cd world
   shp2pgsql tz_world.shp timezones > tz_world.sql

4. Create a temporary database and import the SQL file.

   psql -U postgres -c "CREATE DATABASE timezones" -d template1

   And import the PostGIS functions into the database.
   psql -U postgres -d timezones -f /usr/local/pgsql-9.1/share/contrib/postgis-2.0/postgis.sql

   psql -U postgres -d timezones < tz_world.sql

5. Export the data as text in a simplified format.

   psql -U postgres -d timezones -t -A -c "SELECT tzid, ST_AsText(ST_Simplify(ST_SnapToGrid(geom, 0.01),0.3)) FROM timezones WHERE ST_Area(geom) > 0.8 AND tzid <> 'uninhabited';" > tz_world.txt

6. 