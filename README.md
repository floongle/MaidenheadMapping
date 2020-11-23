# MaidenheadMapping
Using postgis and qgis to render data for Maidenhead


# How to use / update:

(Windows)
1. Clone this repo
1. Install osm2pgsql
1. Clone https://github.com/yannos/Beautiful_OSM_in_QGIS
1. Install postgis https://postgis.net/install/
1. Create a database in postgis (log in using psql shell from the start menu) and create a user:

     ```sql
     CREATE DATABASE maidenhead;
     CREATE USER {username} with encrypted password '{password}';
     GRANT ALL privileges on database maidenhead to {username};
    ```

1. Connect into it to install the postgis extensions
    
    ```sql
    \connect maidenhead;
    CREATE EXTENSION postgis;
    ```

1. Download the latest Berkshire dataset from https://download.geofabrik.de/europe/great-britain/england/berkshire.html - Get the .pbf file. Put it into the sourceData directory
1. Import this .pbf file into postgis using osm2pgsql:

    ```s
    osm2pgsql -U {username} -W -d maidenhead -H 127.0.0.1 --number-processes 24 -C 20480 sourceData/berkshire-latest.osm.pbf
    ```
    (this needs to be run in the directory where osm2pgsql is installed otherwise it will complain about default.style missing)

1. Open QGIS. In the QGIS Browser section, right click on **PostGis** and choose *New Connection* - Fill in the details of the postgis server and username (use the *Basic* tab in the *Auth* section to set username and password) 

1. Render the data on the map: For each of the new items in the **PostGis**/Maidenhead section, (roads, points, polygons, lines) drag and drop them into the layers tab.

1. Now add the styles to the map: For each layer. right click and choose *Properties*. Then at the bottom of the menu that appears, select Style->Load Style. Select the appropriate style file from your cloned "Beatuiful_OSM_in_QGIS" repo and click on "Load Style"

# To create a honeycombe map
1. Install the mmqgis plugin into qgis
1. Select MMQGIS menu->Create->Create Grid Layer
1. Set the size as you need it and choose hexagons. This will create a massive square of hexagons
1. To remove the unneeded hexagons, you'll need a shape of the RBWM.
    1. You can use JOSM, find the RBWM relation, right click on it, select "Download members" and then save that as a .geojson file. Import this file into QGIS. You only need to choose the "Polygon" when importing.
    1. Once added into QGIS, select the hexagon layer again, then choose Vector menu->Research Tools->Select by location
    1. In this dialog, make sure "Selected features from:" is set to your hexagon layer, and make sure that "By comparing to the features from" is set to your .geojson file of RBWM.
    1. You'll now have all the hexagons within RBWM selected. Right Click on the Hexagons layer, select Export->Export Selected Features As. Save this as a new layer "RBWM Hexagons". You can now delete the old "Hexagons" layer.
